> 此文章是翻译[Introducing JSX](https://facebook.github.io/react/docs/introducing-jsx.html)这篇React（版本v15.5.4）官方文档。

## Introducing JSX

考虑这个变量声明：
```jsx
const element = <h1>Hello , world!</h1>
```
这个古怪的标签语法既不是string 也不是HTML。

它就是JSX，它是JavaScript 的一个语法扩展。我们建议在React 中使用它去描述UI 的展示。JSX 可能会使你记起模版语言（a template language），但是它使用JavaScript 的所有功能。

JSX 产生React 的elements。我们将在[下一章](https://facebook.github.io/react/docs/rendering-elements.html) 研究如何将它们渲染为DOM。接下来，你会了解对你使用React 所需要的基本的JSX 知识

### Embedding Expressiongs in JSX

你可以在JSX 中，通过大括号（curly braces）来嵌入任何[JavaScript expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Expressions)。

例如，`2+2`，`user.name` 和`formatName(user)` 都是有效的表达式：
```jsx
function formatName(user){
  return user.firstName + ' ' + user.lastName
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
}

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
)

ReactDOM.render(
  element,
  document.getElementById('root')
)
```
[在CodePen 上尝试](http://codepen.io/gaearon/pen/PGEjdG?editors=0010)

为了可读性我们将JSX 拆分成多行。虽然它不是强制的，当在拆分JSX 为多行时，我们仍然建议使用括号去包括它，避免[automatic semicolon insertion](http://stackoverflow.com/questions/2846283/what-are-the-rules-for-javascripts-automatic-semicolon-insertion-asi) 陷阱。

### JSX is an Expression Too

编译之后，JSX 表达式变成了常规的JavaScript 对象。

也就是说，你可以在`if` 语句和`for` 循环中，将它赋给一个变量，作为一个参数接受它，并从funcitons 中返回。
```jsx
function getGreeting(user){
  if(user){
    return <h1>Hello, {formatName(user)}!</h1>
  }else{
    return <h1>Hello, Stranger</h1>
  }
}
```

### Specifying Attributes with JSX

你可以使用引号来指定字符串属性：
```jsx
const element = <div tabIndex="0"></div>
```
在属性中，你也可以在通过使用大括号来包裹JavaScript 表达式：
```jsx
const element = <img src={user.avatarUrl}></img>
```
在属性中嵌入JavaScript表达式时，不要在大括号上放置引号。 否则，JSX将该属性视为字符串字面量而不是表达式。 你应该使用引号（对于字符串值）或花括号（用于表达式），但不能同时使用在同一属性。

### Specifying Children with JSX

如果一个标签为空，你应该使用`/>` 来闭合标签，类似XML：
```jsx
const element = <img src={user.avatarUrl} />
```
JSX 标签可能包含子节点：
```jsx
const element = (
  <div>
    <h1>Hello </h1>
    <h2>Good to see you here.</h2>
  </div>
)
```
> **附加说明**
由于JSX 更像JavaScript 而不是HTML，因此React DOM 使用`camelCase`  命名习俗来替代HTML 属性名称
例如：在JSX 中，`class`变成`className` ，`tabindex` 变成`tabIndex`。

### JSX Prevents Injection Attacks

在JSX 中嵌入用户输入也是安全的：
```jsx
const title = response.potentiallyMaliciousInput
// This is safe
const element = <h1>{title}</h1>
```
默认，在渲染之前React DOM [转义](http://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-on-html)在JSX 中嵌入的所有值。因此它能确保你永远不能注入任何没有在应用程序明确写入的东西。在渲染之前所有的东西都被转成字符串。这有助于防止(XSS（跨站脚本）)[https://en.wikipedia.org/wiki/Cross-site_scripting]攻击。

### JSX Represents Objects

Babel 将JSX 编译成通过`React.createElement()` 的方法调用：

下面两个例子是完全一样的：
```jsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
)
```
```jsx
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
)
```
`React.createElement()` 执行一些检测来帮助你写出没有bug 的代码，但实际上它创建了如下一个对象：
```jsx
// Note: this structure is simplified
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world'
  }
}
```
这些对象被称为"React elements"。你可以把它们想成描述你想要在屏幕上看的。React 读取这些对象并使用它们构成DOM并保证它们更新。

在下一章中我们将继续探索渲染React elements 为DOM。

> **Tip:**
我们建议在你的编辑器中搜索“Babel”语法格式来使ES6 和JSX 代码正确高亮。
