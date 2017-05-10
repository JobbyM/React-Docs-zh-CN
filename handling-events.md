> 此文章是翻译[handling-events](https://facebook.github.io/react/docs/handling-events.html)这篇React（版本v15.5.4）官方文档。

## Handling Event

React elements 处理事件和DOM elements 事件处理非常类似。只有几个语法不同：

* React events 以camelCase 方式命名，而不是lowercase。
* 在JSX 中以function 作为事件句柄（event handler），而不是字符串

例子，这HTML
```html
<button onclick="activateLasers()">
  Activate Lasers
</button>
```
同React 有不轻微同
```jsx
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

另一个不同就是在React 中你不能通过返回`false` 来阻止默认事件发生。你必须明确的调用`preventDefault`。例如，在纯HTML 中，阻止一个链接跳转打开一个新页面，你可以这样写：
```html
<a href="#" onclick="console.log('The link is clicked.'); return false">Click me</a>
```
在React 中，可以通过以下方式来代替：
```jsx
function ActionLink(){
  function handleClick(e){
    e.preventDefault()
    console.log('The link was clicked.')
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  )
}
```
这里`e` 是一个合成事件（synthetic event）。React 根据[W3C spce](https://www.w3.org/TR/DOM-Level-3-Events/)来定义合成事件（synthetic event），所以你不必担心跨浏览器适配（cross-browser compatibility）问题。参考[`SyntheticEvent`](https://facebook.github.io/react/docs/events.html) 了解更多。

在使用React 时通常你不需要在一个DOM element创建之后，调用`addEventListener`去添加一个侦听器。相反，只需要在element 被初次渲染时添加一个侦听器就可以了。

当你使用[ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 定义一个组件（component）时，通常的做法是在类（class）中添加一个方法作为事件句柄（event handler）。例如，这个`Toggle` 组件（component）就是渲染一个按钮（button）让用户在“ON”和“OFF”状态之间切换。
```jsx
class Toggle extends Component {
  constructor(props){
    super(props)
    this.state = {
      isToggleOn: true
    }

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this)
  }

  handleClick(){
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }))
  }

  render(){
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    )
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
)
```
[在CodePen 上尝试](http://codepen.io/gaearon/pen/xEmzGg?editors=0010)

你必须注意在JSX 回调函数中`this` 的含义。在JavaScript 中，类方法并不是默认绑定的（[bound](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind)）。如果你忘记绑定`this.handleClick` 并把它传递给`onClick`，在实际被调用时，`this` 的值将会是`undefined`。

这并不是React-specific（特性）行为；它只是[how functions work in JavaScript](https://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/)。通常如你引用一个函数并没有带`()`在其函数后面，例如`onClick={this.handleClick}` ，你需要绑定这方法。

如果调用`bind` 令你烦恼，这里有两种方法让你绕开它。如果你已经体验[property initialize syntax](https://babeljs.io/docs/plugins/transform-class-properties/)，你可以使用属性初始化方法（property initializers）去正确的绑定回调函数。
```jsx
class LoggingButton extends Component {
  // This syntax ensures `this` is bound within handleClick
  // Waring: this is *experimental* syntax
  handleClick = () => {
    console.log('This is:', this)
  }

  render(){
    return(
      <button onClick={this.handleClick}>
        Click me
      </button>
    )
  }
}
```
这个语法在[Create React App](https://github.com/facebookincubator/create-react-app) 是默认的。

如果你不使用属性初始化语法（property initializer syntax），你可以在回调函数中使用箭头函数（[arrow function](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)）。
```jsx
class LoggingButton extends Component {
  handleClick(){
    console.log('This is:', this)
  }

  render(){
    // This syntax ensures 'this' is bound within handleClick
    return(
      <button onClick={(e)=>this.handleClick(e)}>
        Click me
      </button>
    )
  }
}
```

这个问题是当`LoggingButton` 每一次被渲染时，都会创建一个不同的回调函数。在大多数情况下，这是可以承受的。然而，如果这个函数被作为prop 传递给子组件（lower components），这些组件可能还会做一次额外的渲染（an extra re-rendering）。我们通常建议在构造函数（contructor）中绑定回调函数来避免这类性能问题。
