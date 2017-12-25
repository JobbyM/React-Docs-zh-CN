> 此文章是翻译[Components and Props](https://reactjs.org/docs/components-and-props.html)这篇React（版本v16.2.0 ）官方文档。

## Components and Props

组件让你将组件分拆独立的、可复用的块，并考虑独立的每一个部分。

从概念上讲，组件类似于JavaScript 函数。它们接受任意的输入（被称为“props”）并且返回React 元素描述在屏幕上如何展示。

### Functional and Class Components

最简单的定义一个组件的方式是写一个JavaScript 函数：

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

这个函数是一个有效的React 组件因为它接受一个“props”（代表属性（properties））对象参数接受数据并且返回一个React 元素。我们称这样的组件为“函数式（funcitional）”因为它们是字面量JavaScript 函数。

你也可以使用[ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 去定义一个组件：

```
class Welcome extends React.Component {
  render(){
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

从React 的View 角度来看上述两个组件是相等的。

类有一些额外的特性，这些特性将在[下一章](https://reactjs.org/docs/state-and-lifecycle.html) 讨论。在此之前，我们将使用函数式组件来简化。

### Rendering a Component

之前，我们只遇到使用React 元素来代表DOM 标签：

```
const element = <div />;
```

然而，元素也可以表示用户自定义组件：

```
const element = <Welcome name="Sara" />;
```

当React 看到一个元素代表一个用户自定义组件时，他将JSX 特性作为一个单独对象传递给组件，我们将这个对象为“props”。

例如，下面代码在页面上渲染“Hello, Sara”：

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

[在CodePen 上尝试](https://reactjs.org/redirect-to-codepen/components-and-props/rendering-a-component)

让我们简要介绍一下这个例子中发生了什么：

1. 我们调用`ReactDOM.render()` 使用`<Welcome name="Sara">` 元素。
2. React 调用`Welcome` 组件将`{name: 'Sara'}` 作为props。
3. 我们的`Welcome` 组件返回一个`<h1>Hello, Sara</h1>` 元素作为结果。
4. React DOM 有效地更新DOM 去匹配`<h1>Hello, Sara</h1> `。


>**附加说明：**

>总是使用大写字母来开始命名一个组件。

>例如，`<div />` 代表一个DOM 标签，但那是`<Welcome />` 代表一个组件，并且需要`Welcome` 在作用域范围内。

### Composing Components

组件可以在其输出中引用其他组件。这让我们可以对任何级别的细节使用相同的组件抽象。按钮、表单、对话框、一屏（screen）：在React 应用中，所有的这些都可以表示为组件。

例如，我们创建一个`App` 组件来渲染多次`Welcome` 组件。

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App(){
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

[在CodePen 上尝试](https://reactjs.org/redirect-to-codepen/components-and-props/composing-components)

通常来说，一个新React 应用在顶部只有一个`App` 组件。 但是，如果你将React 集成到一个现有的应用中，你可能从一个小的组件例如`Button` 来自下而上开始，然后逐渐到视图层的顶级。

### Extracting Components

不必担心将组件拆分成更小的组件。

例如，考虑这个`Comment` 组件：

```
function Comment(props) {
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
}
);
```

[在CodePen 上尝试](https://reactjs.org/redirect-to-codepen/components-and-props/extracting-components)

它接受`author`（一个对象），`text`（一个字符串）、和`date`（一个日期）作为props，来描述一个社交媒体网络评论框。

这个组件去改变很难因为所有的嵌套，也很那去复用它的各个部分。让我们从它提取几个组件。

首先，我们提取一个`Avatar`：

```
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
  )
}
```
这个`Avatar` 并不需要知道它在`Comment` 中被渲染。这是就是为什么我们给他的props 一个更通用的名字：`user` 而不是`author`。

我们建议从组件的自己的视角命名props，而不是从它被使用的上下文环境。

现在我们可以简化一点点`Comment`：

```
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Avatar user={props.author} />
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
};
```

接下来，我们将`Avatar` 和临近它的用户名字提取为`UserInfo` 组件：

```
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.author} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}
```

这能让我们更进一步简化`Comment`：

```
function Comment(props) {
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
  );
}
```

[在CodePen 上尝试](https://reactjs.org/redirect-to-codepen/components-and-props/extracting-components-continued)

一开始提取组件似乎是繁重的工作，但是在一个比较大的应用中会获取很多可复用的组件。一个好的经验法则是，如果你的UI 的一部分被多次使用（`Button`、`Panel`、`Avatar`），或是对自己足够复杂（`App`、`FeedStory`、`Comment`），对一个可复用的组件来说是一个很好的选择。

### Props are Read-Only

不管你使用[as a function or a class](https://reactjs.org/docs/components-and-props.html#functional-and-class-components) 声明一个组件，它决不允许修改它自己的props。考虑下来这个`sum` 函数：

```
function sum(a , b) {
  return a + b;
}
```

这种函数被称为[pure](https://en.wikipedia.org/wiki/Pure_function) 因为它们不被允许修改它们的输入，并且相同输入总是返回相同的结果。

相反，如果它能够修改自己的输入被称为impure：

```
function withdraw(account, amount) {
  account.total -= amount;
}
```

React 是相当的灵活的但是它有一个单一的严格的规则：
**All React Compoents must act like pure functions with respect to their props.**

当然，应用UI 是动态的可以随着时间改变的。在[下一章](https://reactjs.org/docs/state-and-lifecycle.html)中，我们将介绍一个新的概念state。State 允许组件随着事件改变它们的输出响应用户的行为（user action），网络响应（network responses），其他任何事情，而不打破这个规则。
