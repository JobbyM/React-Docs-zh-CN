> 此文章是翻译[JSX In Depth](https://facebook.github.io/react/docs/jsx-in-depth.html)这篇React（版本v15.5.4）官方文档。

## JSX In Depth

从根本上说，JSX 只是为`React.createElement(component, props, ...children)` 函数提供了语法糖（syntactic sugar） 。这种JSX 代码：
```jsx
<MyButton color="blue" shadowSize={2}>
  Click Me
</MyButton>
```
编译成：
```jsx
React.createElement(
  MyButton,
  {color: 'blue', shadowSize: 2},
  'Click Me'
)
```
如果没有子节点你也可以使用自闭合（self-closing）标签形式。像这样：
```jsx
<div className="sidebar" />
```
编译成：
```jsx
React.createElement(
  'div',
  {className: 'sidebar'},
  null
)
```
如果你想要对一些特殊的JSX 转换成JavaScript进行彻底验证，你可以在[the online Babel compiler](https://babeljs.io/repl/#?babili=false&evaluate=true&lineWrap=false&presets=es2015%2Creact%2Cstage-0) 进行试验。

### Specifying The React Element Type

JSX 标签的第一部分决定了React element 的类型（type）。

大写类型（Capitalized types）表明JSX 标签是React component。这些标签直接根据命名变量进行编译，所以如果你使用JSX 的`<Foo />` 表达式，`Foo` 必须在作用域内。

#### React Must Be in Scope

既然编译JSX 需要调用`React.createElement`,这个`React` 库也必须在你的JSX 代码的作用域内。

例如，在代码中都是必须被引入的（import），即使`React` 和`CustomButton` 没有被直接引用：
```jsx
import React from 'react'
import CustomButton from './CustomButton'

function WarningButton(){
  // return React.createElement(CustomButton, {color: 'red'}, ull)
  return <CustomButton color="red"/>
}
```
如果你没有使用JavaScript 打包器（bundler）而是从一个脚本标签（script tag）加载React，React 已经作为一个全局变量在作用域中了。

#### Using Dot Notation for JSX Type

在JSX 中你也可以使用dot-notation 的形式引用一个React component。如果你有一个模块（module）导出了许多React component 是非常方便的。例如，如果`MyComponents.DatePicker` 是一个component，在JSX 中你可以直接使用它：
```jsx
import React from 'react'

const MyComponents = {
  DatePicker: function(props){
    return <div>Imagine a {props.color} datepicker here.</div>
  }
}

function BlueDatePicker(){
  return <MyComponents.DatePicker color="blue" />
}
```

#### User-Defined Components Must Be Capitalized

当一个element 类型是以一个小写字母开始，它表示一个内置（built-in）component 像`<div>` 和`<span>` 是作为`div` 和`span` 这个字符串传入`React.createElement`。类型以大写字母开始就像`<Foo />` 编译到`React.createElement(Foo)` 并且响应到一个component 定义或者在一个JavaScript 文件中被引入。

我们建议使用大写字母来命名components。如果你有一个component 是以小写字母开始的，在JSX 中使用之前需要将其赋给一个大写字母开头的变量（a capitalized variable）。

例如，此代码不会像预期的那样运行：
```jsx
import React from 'react'

// Wrong! This is a component and should have been capitalized
function hell(props){
  // Correct! This use of <div> is legitimate because div is a valid HTML tag:
  return <div>Hello {props.toWhat}</div>
}

function HelloWorld(){
  // Wrong! React thinks <hello /> is an HTML tag because it's not captialized;
  return <hello toWhat="World" />
}
```
为了解决它，我们需要将`hello` 重名为`Hello` 同时使用`<Hello />` 来引用它：
```jsx
import React from 'react'

// Correct! This is a component and should have been capitalized
function Hello(props){
  // Correct! This use of <div> is legitimate because div is a valid HTML tag:
  return <div>Hello {props.toWhat}</div>
}

function HelloWorld(){
  // Correct! React knows <Hello /> is a component because it's not captialized;
  return <Hello toWhat="World" />
}
```

#### Choosing the Type at Runtime

你不能使用一般的表达式作为React element 类型。如果需要使用一个一般的表达式只是element 的类型，只需要将其赋值给一个大写字母开头的变量。这通常在你需要基于一个props 去渲染不同的component 时发生：
```jsx
import React from 'react'
import { PhotoStory, VideoStory } from './stories'

const components = {
  photo: PhotoStory,
  vidoe: VideoStory
}

function Story(props){
  // Wrong! JSX type can't be an express
  return <components[props.storyType] story={props.story} />
}
```
为了解决它，我们将其赋值给一个大写字母开头的变量：
```jsx
import React from 'react'
import { PhotoStory, VideoStory } from './stories'

const components = {
  photo: PhotoStory,
  vidoe: VideoStory
}

function Story(props){
  // Correct! JSX type can be a captialized variable
  const SpecificStory = components[props.storyType]
  return <SpecificStory story={props.story} />
}
```

### Props in JSX

在JSX 中有几种不同的方式去规定props。

#### JavaScript Expressions

你可以使用`{}`将任意JavaScript 表达式作为props 传入。例如，在JSX 中：
```jsx
<MyComponents foo={1 + 2 + 3 + 4} />
```
对于`MyComponent`，这个`props.foo` 的值将是计算表达式`1 + 2 + 3 + 4` 得到的结果`10`。

在JavaScript 中`if` 语句和`foo` 循环不是表达式，所以它们不能直接在JSX 中使用。 相反，你需要将它们在代码中使用。例如：
```jsx
function NumberDescriber(props){
  let description
  if (props.number % 2 == 0) {
    description = <strong>even</strong>
  } else {
    description = <i>odd</i>
  }
  return <div>{props.number} is an {description} number</div>
}
```
你可以在[conditional rendering](https://facebook.github.io/react/docs/conditional-rendering.html) 和[loops](https://facebook.github.io/react/docs/lists-and-keys.html) 相关章节了解更多。

#### String Literals

你可以将字符串字面量作为props 传入。下面两种JSX 表达式是等价的：
```jsx
<MyComponent message="hello world" />

<MyComponent message={'hello world'} />
```
当你传入一个字符串字面量时，它的只是HTML 转义（HTML-unescaped）。所以下面两种JSX 表达式是等价的：
```jsx
<MyComponent message="&lt;3" />

<MyComponent message={'<3'} />
```
这两中行为并不相关。在这里只是为完整性而被提及。

#### Props Default to "True"

如果你没有为一个prop 传值，它的默认值是`true`。下面两种JSX 表达式是等价的：
```jsx
<MyTextBox autocomplete />

<MyTextBox autocomplete={true} />
```
通常，我们不建议使用这种方式因为这会使[ES6 object shorthand]() `{foo}` 是`{foo:foo}` 的简写而不是`{foo:true}` 产生困惑。这种行为在这只是为了匹配HTML的行为。

#### Spread Attribtes

如果你已经有了对象作为`props`，并且你想将在JSX中传入，你可以使用`...` 这种的“spread” operator 将整个props 对象传入。下面两种components 是等价的：
```jsx
function App1(){
  return <Greeting firstname="Ben" lastName="Hector" />
}

function App2(){
  conost props = {firstname: 'Ben', lastName='Hector'}
  return <Greeting {...props} />
}
```
Spread attributes 是非常有用的当你构建一般容器（generic containers）。但是，由于非常容易将不相关的props 传入components 而不关心它们会使你的代码非常混乱。我们建议你谨慎使用这种语法。

### Children in JSX

在JSX 表达式可以包含在一个开合标签和一个闭合标签中，这些内容可以通过一个特殊的prop 传入：`props.children`。有几种不同的方式来传入子节点：

#### String Literals

你可以在开合标签和闭合标签中传入一个字符串，并且`props.children` 可以是那个字符串。这对多数HTML  内置element 是有用的。例如：
```jsx
<MyComponent>Hello World</MyComponent>
```
这是有效的JSX，并且在`MyComponent` 中的`props.children` 是这个`Hello world!` 字符串。HTML 是转义的，所以你可以像写HTML 方式写JSX：
```jsx
<div>This is valid HTML &amp; JSX at the same time.</div>
```
JSX 会移除一行中开始和结尾的空格。它也移除空行。邻接标签的新行也会被移除；新行在字符串中间出现也会变成一个空格。所以下面这些都会渲染成一样：
```jsx
<div>Hello World</div>

<div>
  Hello World
</div>

<div>
  Hello
  World
</div>

<div>

  Hello World
</div>
```

#### JSX Children

你可以提供更多的JSX elements 作为子节点。这对于嵌套的components 是非常有用的：
```jsx
<MyContainer>
  <MyFirstComponent />
  <MySecondComponent />
</MyContainer>
```
你可以混合不同类型的子节点，所以你可以和JSX 子节点一起使用字符串字面量。这是另一种类似HTML的JSX，所以它们都是有效的JSX 和HTML：
```jsx
<div>
  Here is a list:
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
  </ul>
</div>
```
一个React component 不能返回多个React elements，但是一个JSX 表达式可以有多个子节点，所以如果你要在一个component 中渲染跟多东西，你可以将其包裹在一个`div` 中。

#### JavaScript Expressions as Children

你要将任何JavaScript 表达式通过包裹在`{}` 中将其作为子节点传入。例如：所以下面两种JSX 表达式是等价的：
```jsx
<MyComponent>foo</MyComponent>

<MyComponent>{'foo'}</MyComponent>
```
这对渲染任意长度的JavaScript 表达式列表是非常有用的。例如：下面渲染一个HTML 列表：
```jsx
function Item(props){
  return <li>{props.message}</li>
}

function TodoList(){
  const todos = ['finish doc', 'submit pr', 'nag dan to review']
  return (
    <ul>
      {todos.map((message) => <Item key={message} message={message} />)}
    </ul>
  )
}
```
JavaScript 表达式可以和其它类型的子节点混合。它经常用来代替字符串模版（string templates）：
```jsx
function Hello(props){
  return <div>Hello {props.addressee}</div>
}
```

#### Functions as Children

通常，在JSX 中的JavaScript 表达式被计算成字符串、React element 或者是列表中这些东西。然后，`props.children` 工作就像任何其它的prop ，它可以传入任何类型的数据，不仅仅是React 知道如何去渲染的类型。例如，你有一个自定义的component，你可以将props.children 作为一个回调函数传入：
```jsx
function ListOfTenThings(){
  return (
    <Repeat numTimes={10}>
      {(index) => <div key={index}>This is item {index} in the list</div>}
    </Repeat>
  )
}

// Calls the children callback numTimes to produce a repeated component
function Repeat(props){
  let items = []
  for (let i = 0; i < props.numTimes; i++ ) {
    items.push(props.children(i))
  }
  return <div>{items}</div>
}
```
子节点可以掺入将任何东西传入自定义component，只要component 在渲染之前将其转成React 可以理解的东西。这种使用方式并不普通，但是如果你想要扩展JSX 的能力时是非常有用的。

#### Booleans, Null, and Undefined Are Ignored

`false`、`null`、`undefined`、和`true` 是有效的子节点。 它们只不过都不渲染。这些JSX 表达式都渲染成同一样东西：
```jsx
<div />

<div></div>

<div>{false}</div>

<div>{null}</div>

<div>{true}</div>
```
它们能够条件渲染React elements。如果`showHeader`为`true`那么只渲染`<Header />`：
```jsx
<div>
  {showHeader && <Header />}
  <Content />
</div>
```
一个附加说明就是["falsy" values](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) 就像`0` number 仍然可以被React 渲染。例如，下面的代码不会按照你期待的那样运行，因为`0` 会被打印当`props.messages` 是一个空数组时：
```jsx
<div>
  {props.messages.length &&
    <MessageList messages={props.messages}
  }
</div>
```
为了解决它，请确保`&&`之前的表达式总是为布尔值：
```jsx
<div>
  {props.messages.length > 0 &&
    <MessageList messages={props.messages}
  }
</div>
```
相反的，如果你想将`false`、`true`、`null` 或者是`undefined` 作为一个值展示出来，你可以使用[convert it to string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String#String_conversion)：
```jsx
  <div>
    My JavaScript variable is {String(myVariable)}.
  </div>
```
