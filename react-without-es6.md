> 此文章是翻译[React Without ES6](https://facebook.github.io/react/docs/react-without-es6.html)这篇React（版本v15.4.0）官方文档。

## React Without ES6

通常你可以使用一个纯JavaScript class 去定义一个React component：
```jsx
class Greeting extends Component {
  render(){
    return <h1>Hello, {this.props.name}</h1>
  }
}
```
如果你不使用ES6，你也可以使用`React.createClass` 来代替：
```jsx
var Greeting = React.createClass({
  render: function(){
    return <h1>Hello, {this.props.name}</h1>
  }
})
```
ES6 class API 的使用除了几个例外和`React.createClass` 非常相似。

### Declaring Prop Types and Default Props

使用 functions 和ES6 classes，`propTypes`和`defaultProps`被定义为component 自身的属性：
```jsx
class Greeting extends Component {
  render(){
    return <h1>Hello, {this.props.name}</h1>
  }
}

Greeting.propTypes = {
  name: React.PropTypes.string
}

Greeting.defaultProps = {
  name: 'Mary'
}
```
使用`React.createClass` ，你需要定义`propTypes` 作为一个属性传入对象，而`getDefaultProps` 作为一个方法：
```jsx
var Greeting = React.createClass({
  propTypes: {
    name: React.PropTypes.string
  }

  getDefaultProps: function(){
    return {
      name: 'Mary'
    }
  }

  render: function(){
    return <h1>Hello, {this.props.name}</h1>
  }
})
```
### Setting the Initial State

在ES6 classe 中，你可以在constructor 通过给`this.state` 赋值来定义初始state：
```jsx
class Greeting extends Component {
  constructor(props){
    super(props)
    this.state = {
      count: props.initialCount
    }
  }
  // ...
}
```
使用`React.createClass()`，你必须提供一个独立的`getInitialState` 方法去返回初始state：
```jsx
var Greeting = React.createClass({
  getInitialState: function(){
    return {
      count: this.props.initalCount
    }
  }

 // ...
})
```

### Autobinding

在React component 中使用ES6 classes 声明，方法和正规ES6 classes 语法一样。这意味着它们不会自动绑定`this` 到实例上。你必须在constructor 中明确地使用`.bind(this)`：
```jsx
class SayHello extends Component {
  constructor(props){
    super(props)
    // This line is important!
    this.handleClick = this.handleClick.bind(this)
  }
  handleClick(){
    alert('Hello!')
  }
  render(){
    // Becase `this.handleClick` is bound, we can use it as an event handler
    return (
      <button onClick={this.handleClick}>
        say hello
      </button>
    )
  }
}
```

使用`React.createClass()` ，这就不是必须的，因为它在所有的方法都绑定了：
```jsx
var SayHello = React.createClass({
  handleClick: function(){
    alert('Hello!')
  }
  render: function(){
    return (
      <button onClick={this.handleClick}>
        say hello
      </button>
    )
  }
})
```
这意味着写ES6 classess 会遇到更多一点的样板代码对事件句柄，好处是是在大型应用中有更好的性能。

如果模版代码不吸引你，你可以使用Babel 的实验性Class 属性一份提议：
```jsx
class SayHello extends Component {
  // WARNING: this syntax is experimental!
  // Using an arrow heer binds the method
  handleClick = () => {
    alert('Hello!')
  }
  render(){
    return (
      <button onClick={this.handleClick}>
        say hello
      </button>
    )
  }
}
```
请注意上述的一份是**实验性的（experimental）** 并且语法可能会变化，或者这个提议不会进入语言。

如果你更安全的使用它，你有一下几个选择：

* 在constructor 中绑定方法。
* 使用箭头函数，`onClick={(e)=>this.handleClick(e)}`。
* 保持使用`React.createClass()`。


### Mixins

>**Notes**
ES6 语言不再支持任何混合（mixin）。因此，当你在React 使用ES6 classes 时，也不支持混合。
**我们在使用混合的代码库中发现大量问题**[不建议在新代码中使用它们](https://facebook.github.io/react/blog/2016/07/13/mixins-considered-harmful.html)
这章只是为了参考而存在。

有时不同的component 会共享相同的功能（funcitional）。这称为[cross-cutting concerns](https://en.wikipedia.org/wiki/Cross-cutting_concern)。`React.createClass` 让你合法的使用`mixins` 在这种情况下。

一个常见的用例是在时间间隔上更新component 自身。使用`setInterval()` 是简单的，但是当你不再使用它去保存记忆时，去取消它是重要的。React 提供[lifecycle methods](https://facebook.github.io/react/docs/state-and-lifecycle.html)去让你知道什么去创建或销毁一个component。让我们创建一个简单的mixin 使用这些方法去提供一个简单的`setInterval()` 方法去自动清理当你的component 被销毁时：
```jsx
var SetIntervalMixin = {
  componentDidMount: function(){
    this.intervals = []
  },
  setInterval: function(){
    this.intervals.push(setInterval.apply(null, arguments))
  },
  componentWillUnmount: function(){
    this.intervals.forEach(clearInterval)
  }
}

var TickTock = React.createClass({
  mixins: [SetIntervalMixin], // use the mixin
  getInitialState: function(){
    return {seconds: 0}
  },
  componentDidMount: function(){
    this.setInterval(this.tick, 1000) // Call a method on the mixin
  },
  tick: function(){
    this.setState({
      seconds: this.state.seconds + 1
    })
  },
  render: function(){
    return (
      <p>
        React has been running for {this.state.seconds} seconds.
      </p>
    )
  }
})

ReactDOM.render(
  <TickTock />,
  document.getElementById('root')
)
```
如果一个component 使用复杂的mixins 或几个mixins 定义同一个lifecycle 方法（例如：几个mixins 方法想要在component 销毁时进行清理工作），所有的lifecycle 方法都会保证被调用。方法被定义mixins 上去按照mixins 列表中的顺序运行，随着component 中的方法被调用。
