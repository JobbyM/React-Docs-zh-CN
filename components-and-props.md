> 此文章是翻译[Components and Props](https://facebook.github.io/react/docs/components-and-props.html)这篇React（版本v15.4.0）官方文档。

## Components and Props

Components 让您将组件分拆独立的、可复用的块，并考虑独立的每一个部分。

从概念上讲，components 类似于JavaScript 函数。它们接受任意的输入（被称为“props”）并且返回React elements 描述在屏幕上如何展示。

### Functional and Class Components

最简单的定义一个component 的方式是写一个JavaScript 函数：
```jsx
function Welcome(props){
  return <h1>Hello, {props.name}</h1>
}
```
这个函数是一个有效的React component因为它接受一个“props”对象参数接受数据并且返回一个React element。我们称这样的component 为“funcitional”因为它们是字面量JavaScript 函数。

你也可以使用[ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 去定义一个component：
```jsx
class Welcome extends Component {
  render(){
    return <h1>Hello, {this.props.name}</h1>
  }
}
```
从React 角度来看上述两个component 是相等的。

Classes 有一些额外的特性，这些特性将在[下一章](https://facebook.github.io/react/docs/state-and-lifecycle.html) 讨论。

到那时，为了间接性我们将使用functional components 。

### Rendering a Component

之前，我们只遇到使用React elements 来代表DOM 标签：
```jsx
const element = <div />
```
然而，elements 也可以表示用户自定义（user-defined）components：
```jsx
const element = <Welcome name="Sara" />
```
当我们看打一个element 代表一个用户自定义component 时，他将JSX attributes 作为一个单独对象传递给component，我们将这个对象为“props”。

例如，下面代码在页面上返回“Hello, Sara”：
```jsx
function Welcome(props){
  return <h1>Hello, {props.name}</h1>
}

const element = <Welcome name="Sara" />

ReactDOM.render(
  element,
  document.getElementById('root')
)
```
让我们简要介绍一下这个例子中发生了什么：

	1. 我们调用`ReactDOM.render()` 方法中使用`<Welcome name="Sara">` element。
	2. React 调用`Welcome` component 将`{name: 'Sara'}` 作为props。
	3. 我们的`Welcome` component 返回一个`<h1>Hello, Sara</h1>` element 作为结果。
	4. React DOM 有效地更新DOM 去匹配`<h1>Hello, Sara</h1> `。


>**附加说明：**
使用大写字母来命令一个component。
例如，`<div />` 代表一个DOM 标签，但那是`<Welcome />` 代表一个component，并且需要`Welcome` 在作用域范围内。


### Composing Components

Components 可以在其他components 输出中被引用。这让我们使用相同的在任何细节上抽象的组件。按钮、表单、对话框、一屏（screen）：在React 应用中，所有的这些都可以表示为components。

例如，我们创建一个`App` component 来渲染多次`Welcome` component。
```jsx
function Welcome(props){
  return <h1>Hello, {props.name}</h1>
}


function App(){
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  )
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
)
```
通常来说，一个新React 应用在顶部只有一个`App` component。 但是，如果你将React 集成到一个现有的应用中，你可能从一个小的component 例如`Button` 来自下而上开始，然后逐渐到视同层的顶级。

>**附加说明：**
Components 必须返回一个单独的root element。这是为什么我们要使用`<div>` 将所有的`<Welcome />` element 包裹起来。

### Extracting Components

不必担心将components 拆分成更小的components。

例如，考虑这个`Comment` component：
```jsx
function Comment(props){
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  )
}
```
它接受`author`（一个对象），`text`（一个字符串）、和`date`（一个日期）来描述一个社交媒体网络评论框。

这个component 去改变很难因为所有的嵌套，也很那去复用它的各个部分。让我们从它提起几个components。

首先，我们提取一个`Avatar`：
```jsx
function Author(props){
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
  )
}
```
这个`Avatar` 并不需要知道它在`Comment` 中被渲染。这是就是为什么我们给他的props 一个更通用的名字：`user` 而不是`author`。

我们建议在命名props 是从component 的角度触发而不是从它被使用的环境。

现在我们可以简化一点点`Comment`：
```jsx
function Comment(props){
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Author user={props.author} />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  )
}
```
接下来，我们将`Avatar` 和临近它的用户名字提取为`UserInfo` component：
```jsx
function UserInfo(props){
  <div className="UserInfo">
    <Author user={props.author} />
    <div className="UserInfo-name">
      {props.user.name}
    </div>
  </div>
}
```
这能让我们更进一步简化`Comment`：
```jsx
function Comment(props){
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  )
}
```
一开始提取components 似乎是繁重的工作，但是在一个比较大的应用中会获取很多可复用的components。一个好的经验法则是，如果你的UI 的一部分被多次使用（`Button`、`Panel`、`Avatar`），或是对自己不够复杂（`App`、`FeedStory`、`Comment`），对一个可复用的compoent 来说是一个很好的选择。

### Props are Read-Only

不管你使用[as a function or a class](https://facebook.github.io/react/docs/components-and-props.html#functional-and-class-components) 声明一个component，它决不允许修改它自己的props。考虑下来这个`sum` 函数：
```jsx
function sum(a , b){
  return a + b
}
```
这种函数被称为[pure](https://en.wikipedia.org/wiki/Pure_function) 因为它们不被允许修改它们的输入，并且相同输入总是返回相同的结果。

相反，如果它能够吸怪自己的输入被称为impure：
```jsx
function withdraw(account, amount){
  account.total -= amount
}
```
React 是相当的灵活的但是它有一个单一的严格的规则：
**All React Compoents must act like pure functions with respect to theire props.**

当然，应用UI 是动态的可以随着时间改变的。在[下一章](https://facebook.github.io/react/docs/state-and-lifecycle.html)中，我们将介绍一个新的概念state。State 允许components 随着事件改变它们的输出响应用户的行为（user action），网络响应（network responses），其他任何事情而不打破这个规则。
