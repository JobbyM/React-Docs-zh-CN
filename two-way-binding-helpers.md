> 此文章是翻译[Two-way Binding Helpers](https://facebook.github.io/react/docs/two-way-binding-helpers.html)这篇React（版本v15.4.0）官方文档。

## Two-way Binding Helpers

>**注意：`LinkedStateMixin` 在React v15 被废弃掉了。建议明确地设置value 以及更改句柄，而是不是用`LinkedStateMixin`。**

**Importing**

```jsx
import LinkedStateMixin  from 'react-addons-linked-state-mixin' // ES6
var LinkedStateMixin = require('react-addons-linked-state-mixin') // ES5 with npm
var LinkedStateMixin = React.addons.LinkedStateMixin; // ES5 with react-with-addons.js
```

## Overview

`LinkedStateMixin` 是表达React 的双向绑定的一种简单方法。

在React 中，数据流是单向的：从拥有者到孩子。这是因为在[the Von Neumann model of computing]() 中数据只能向一个方向流动。你可以认为它是“单向绑定的”。

然而，需要引诱需要你去读取数据并将其返回到你的程序中。例如，当开发表单时，当你收到用户的输入，你需要更新React 的`state`。或者可能你想要在JavaScript 中执行布局去对DOM element 大小的改变做出响应。

在React 中，你将可以通过监听“change”事件，读取从你的数据源（通常是DOM），在你的component 中的一个上调用`setState()` 去实现它。“关闭数据流循环 （closing the data flow loop）”明确地导致更容易理解和更容易维护程序。查看[our form documentation](https://facebook.github.io/react/docs/forms.html)获取更多信息。

双向绑定 -- 隐式执行DOM 中的一些值总是同一些React `state` 保持一致 --  是简洁的，支持多种应用。我们已经提供了`LinkedStateMixin`：上述设置通过数据流动模式的语法它，或者“链接（linking）”一些数据源到React 的`state`。

>**Note：**
`LinkedStateMixin` 只是一个简单的包裹器， `onChange/setState()` 模式的规则。它并没有从根本上改变在你的React 应用中数据流动。

 ## LinkedStateMixin: Before and After

这是一个不使用`LinkedStateMixin` 的简单的表单例子：
```jsx
var NoLink = React.createClass({
  getInitialState: function(){
    return {message: 'Hello!'}
  },
  handleChange: function(event){
    this.setState({message: event.target.value})
  },
  render: function(){
    var message = this.state.message
    return <input type="text" value={message} onChange={this.handleChange}/>
  }
})
```
这工作的非常好并且数据的流动也非常清晰，然后，如果有许多表单域，它可能会有点冗余。使用`LinkedStateMixin` 去减少打字：
```jsx
var WithLink = React.createClass({
  mixins: [LinkedStateMixin],
  getInitialState: function(){
    return {message: 'Hello!'}
  },
  render: function(){
    return <input type="text" valueLink={this.linkState('message')}/>
  }
})
```
`LinkedStateMixin` 添加了`linkState()` 方法到你的React component 中。`linkState()` 返回了一个包含当前React state 的值的`valueLink` 对象，一个回调函数去改变它。

`valueLink` 对象可以作为props, 在向下或向上传入树中，所以它很容易的（明确的）在一个深入层级的component和位于高层级的state之间设置双向绑定。

注意到复选框（checkbox）有一个特殊的行为关于它们的`value` 特性（attribute），这个值将会在表单提交的时候送出如果这个复选框被选定（默认是`on`）。`value` 特性不被更新当复选框被选定或没有选定。对于复选框，你应该使用`checkedLink` 替代`valueLink`：`<input type="checkbox" checkedLink={this.linkState('booleanValue')}/>`。

## Under the Hood

`LinkedStateMixin` 有两个方面：你创建`valueLink` 实例的位置和你使用它的位置。为了证明`LinkedStateMixin` 是多么简单，让我们分开重写每一方面使它更清晰。

### valueLink Without LinkedStateMixin

```jsx
var WithoutMixin = React.createClass({
  getInitialState: function(){
    return {message: 'Hello'}
  },
  handleChange: function(newValue){
    this.setState({message: newValue})
  },
  render: function(){
    var valueLink = {
      value: this.state.message,
      requestChange: this.handleChange
    }
    return <input type="text" valueLink={valueLink}/>
  }
})
```
就像你看到的那样，`valueLink` 对象是一个非常简单的对象，它值有一个`value` 和一个`requestChange` prop。和`LinkedStateMixin` 是同样的简单：它只是使用来自`this.state` 的值和一个调用`this.setState()` 的回调函数填充这些字段。

### LinkedStateMixin Without valueLink

```jsx
var WithoutValue = React.createClass({
  mixins: [LinkedStateMixin],
  getInitialState: function(){
    return {message: 'Hello!'}
  },
  render: function(){
    var valueLink = this.linkState('message')
    var handleChange = function(e){
      valueLink.requestChange(e.target.value)
    }
    return <input type="text" value={valueLink.value} onChange={handleChange}/>
  }
})
```
`valueLink` prop 也是相当的简单。它简单地控制`onChange` 事件，调用`this.props.valueLink.requestChange()`，也使用`this.props.valueLink.value` 替代`this.props.value` 。好了！
