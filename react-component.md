> 此文章是翻译[React.Component](https://facebook.github.io/react/docs/react-component.html)这篇React（版本v15.4.0）官方文档。

## React.Component

[Components](https://facebook.github.io/react/docs/components-and-props.html)让你将UI 分成独立的、可复用的块，并且独立的思考每一块。`React.Component` 是由[React](https://facebook.github.io/react/docs/react-api.html)提供。

## Overview

`React.Component` 是一个抽象的基类，所以它几乎很少去直接引用`React.Component`。相反，通常你将它作为一个子类，并定义至少一个`render()` 方法。

通常你看可以使用纯[JavaScript class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 去定义一个React component：
```jsx
class Greeting extends React.Component {
  render(){
    return <h1>Hello, {this.props.name}</h1>
  }
}
```
如果你不曾使用ES6，你可以使用[React.createClass](https://facebook.github.io/react/docs/react-api.html#createclass)帮助类来代替。参考[Using React without ES6](https://facebook.github.io/react/docs/react-without-es6.html) 去了解更多。

### The Component Lifecycle

每一个component 都有几个“lifecycle methods” 你可以在进程的特殊时间去重写去运行代码。方法是**will**前缀表示在某些事情发生前调用，方法是**did**前缀表示在某些事情发生后调用。

#### Mounting

当component 的实例已经被创建并被插入到DOM 时被调用：

* [constructor()](https://facebook.github.io/react/docs/react-component.html#constructor)
* [componentWillMount()](https://facebook.github.io/react/docs/react-component.html#componentwillmount)
* [render()](https://facebook.github.io/react/docs/react-component.html#render)
* [componentDidMount()](https://facebook.github.io/react/docs/react-component.html#componentdidmount)


#### Updating

props 或state 的改变导致产生update。当component 正在重新渲染（re-rendered）时，下面方法被调用：

* [componentWillReceiveProps()](https://facebook.github.io/react/docs/react-component.html#componentwillreceiveprops)
* [shouldComponentUpdate()](https://facebook.github.io/react/docs/react-component.html#shouldcomponentupdate)
* [componentWillUpdate()](https://facebook.github.io/react/docs/react-component.html#componentwillupdate)
* [render()](https://facebook.github.io/react/docs/react-component.html#render)
* [componentDidUpdate()](https://facebook.github.io/react/docs/react-component.html#componentdidupdate)


#### Unmounting

当component 从DOM 中移除时这个方法被调用：

* [componentWillUnmount()](https://facebook.github.io/react/docs/react-component.html#componentwillunmount)


### Other APIs

每一个component 也提供以下几个APIs：

* [setState()](https://facebook.github.io/react/docs/react-component.html#setstate)
* [forceUpdate()](https://facebook.github.io/react/docs/react-component.html#forceupdate)


### Class Properties

* [defaultProps](https://facebook.github.io/react/docs/react-component.html#defaultprops)
* [displayName](https://facebook.github.io/react/docs/react-component.html#displayname)
* [propTypes](https://facebook.github.io/react/docs/react-component.html#proptypes)


### Instance Properties

* [props](https://facebook.github.io/react/docs/react-component.html#props)
* [state](https://facebook.github.io/react/docs/react-component.html#state)


## Reference

### render()

```jsx
render()
```

`render()` 方法是必须的。

当调用是，它应该检测`this.props` 和`this.state` 并返回一个React element。element 可以是代表一个本地的DOM component，就像`<div />`，也可以是一个用你自己定义的component 合成的component。

你可以返回`null` 或者是`false` 代表你不想渲染任何东西。打死那个返回是`null` 或者是`false` 时，`ReactDOM.findDOMNode(this)` 将会返回`null`。

`render()` 方法应该是纯粹的，意味着你它不会修改component state，每次被调用都会返回相同的结果，并且它并不直接和浏览器交互。如果你需要同浏览器交互，在`componentDidMount()` 或其它lifecycle methods 中执行你的操作。 保持`render()` 使component 更容易去思考

>**Note**
如果`shouldComponentUpdate()` 方法返回false，`render()` 不会被调用。

### constructor

```jsx
constructor(props)
```
在它被加载（mounted）前，React component 的constructor 被调用。当实现`React.Component` 子类的constructor 方法时，你应该在其它任何语句之前调用`super(props)`，否则，`this.props` 在constructor 中是undefined ，这将导致bug。

在constructor 中正确初始化state。如果你不初始化state 并且不绑定方法，你不必为你的React component 去实现这个constructor。

如果你知道你要做什么，使用props 去初始化state 是正确的。这里有一个有效的`React.Component` 子类的constructor：
```jsx
constructor(props){
    super(props)
    this.state = {
      color: props.initalColor
    }
  }
```
谨防这种形式，因为它有效地“fork”props 会导致bugs 。而不是同步props 到state，你经常想要提升state。

如果你为了state 使用它们来“fork” props，你可以也想要实现`componentWillReceiveProps(nextProps)` 去保持state 和它们同步更新。但是提升state 通常是更容易的并且很少产生错误。

### componentWillMount()

```jsx
componentWillMount()
```
`componentWillMount()` 在加载（mounting） 发生之前被立即调用。它在`render()` 之前被调用，因此在这个方法中设置state 将不会触发重新渲染。

避免在这个方法中引入任何副作用或订阅。

### componentDidMount()

```jsx
componentDidMount()
```
`componentDidMount()` 在当component 被加载（mounted）时被立即调用。初始化需要DOM node 已经在这里了。如果你需要从远端（remote endpoint）去加载数据，这里实例化一个网络请求是正确的。在这个方法中设置state 将会触发重新渲染。

### componentWillReceiveProps()

```jsx
componentWillRecevieProps(nextProps)
```
当加载的（mounted）component 接受新的props 之前`componentWillReceiveProps()` 方法被调用。如果你需要更新state 去响应prop 改变（例如，重置它），你可以对比`this.props` 和`nextProps` 并在这个方法中使用`this.setState()` 去执行state 改变。

注意当props 还没有发生改变，React 也可以调用这个方法， 所以如果你只像处理变化，请确保去比较当前值和下一个值。当父 component 导致你的component 重新渲染时，可能会发生这种状况。

如果你只是调用`this.setState()`，`componentWillReceiveProps()` 将不会被调用。

### shouldComponentUpdate()

```jsx
shouldComponentUpdate(nextProps, nextState)
```
使用`shouldComponentUpdate()` 让React 了解如果一个component 的输出不被当前state 或props 的改变影响。默认行为是在每一次state 改变时，都重新渲染，并且在大部分情况下，你应该依赖默认行为。

当新的props 或state 被接受时，`shouldComponentUpdate()` 在渲染之前被调用。默认是`true`。在一次渲染后者当`forceUpdate()` 被使用时，这个方法不被调用。

当它们的state 改变时，返回`false` 不能阻止子component 重新渲染。

当前，如果`shouldComponentUpdate()`  返回`false`，那么[componentWillUpdate()](https://facebook.github.io/react/docs/react-component.html#componentwillupdate) ，[render()](https://facebook.github.io/react/docs/react-component.html#render) 和[componentDidUpdate()](https://facebook.github.io/react/docs/react-component.html#componentdidupdate) 都不会被调用。注意在未来React 可能将`shouldComponentUpdate()`作为一个提示而不是一个严格的指令，并且返回`false` 仍然导致component 重新渲染。

如果你在分析后确定特定的component 是慢的，你可以改变它去继承[React.PureComponent](https://facebook.github.io/react/docs/react-api.html#react.purecomponent)，而它实现了一个浅prop和state 比较的`shouldComponentUpdate()`方法。 如果你有信息去亲自实现它，你可以比较`this.props` 和`nextProps` 以及`this.state` 和`nextState` 并返回`false` 告诉React 这次更新可以被跳过。

### componentWillUpdate()

```jsx
componentWillUpdate(nextProps, nextState)
```
当新的props 或state 正在被接受，`componentWillUpdate()`在渲染之前将立即被调用。在更新发生之前使用这个方法作为一个机会去执行这个准备。这个方法在第一次渲染时不会被调用。

注意你不能在这里调用`this.setState()`。如果你需要更新state 去响应prop 改变，使用`componentWillReceiveProps()` 来代替。

>**Note**
如果[shouldComponentUpdate()](https://facebook.github.io/react/docs/react-component.html#shouldcomponentupdate) 返回false，`componentWillUpdate()` 将不会被调用。

### componentDidUpdate()

```jsx
componentDidUpdate(prevProps, prevState)
```
当更新反生后`componentDidUpdate()`立即被调用。这个方法在第一次渲染时不会被调用。

当component 已经被更新时，使用这个方法有机会去操作DOM。这也是一个好地方去做网络请求只要你对比当前props 和之前props（例如，如果props 还没有发生改变，网络请求可能不是必须的）。

>**Note**
如果[shouldComponentUpdate()](https://facebook.github.io/react/docs/react-component.html#shouldcomponentupdate) 返回false，`componentDidUpdate()` 将不会被调用。

### componentWillUnmount()

```jsx
componentWillUnmount()
```
在component 被卸载（unmount）或销毁之前，`componentWillUnmount()`立即被调用。在这个方法中执行必须的清理，例如，使定时器无效，取消网络请求或清楚在`componentDidMount()` 中创建的任何DOM
 element。

### setState()

```jsx
setState(nextState, callback)
```
将当前state 和下一个state 执行一个浅合并（shallow merge）。这个是主要的方法，你使用它去触发UI 更新从事件句柄和服务器请求回调中。

第一个参数可以是一个对象（包含0个或多个key 去更新）或者是一个函数（state 和props）可以返回一个对象包含keys 去更新。

这有一个简单对象用例：
```jsx
this.setState({mykey: 'my new value'})
```
这也可能使用这个签名函数`function(state, props) => newState` 传入。这个入队的原子更新在设置任何值之前需要分析之前的state 和props 的值。例如，假设我们想要通过`props.step` 来增加state 的值：
```jsx
this.setState((prevState, props)=>{
    return {myInteger: prevState.myInteger + props.step}
  })
```
第二参数是一个可选的回调函数，一旦`setState` 执行完成并且component 已经重新渲染时这个回调函数将会被执行。通常我们建议使用`componentDidUpdate()` 来代替执行这种逻辑。

`setState()`不会理解使`this.state` 发生改变而是创建一个挂起（pending）的state 转换。调用这个方法之后访问`this.state` 可能返回当前存在的值。

没有保证调用`setState` 的成批的同步操作会有性能增益。

`setState()` 将总会导致重新渲染除非`shouldComponentUpdate()` 返回`false`。如果可变对象正在被使用和条件渲染逻辑不能在`shouldComponentUpdate()` 中被实现，只有当新的state 不同于之前state 时才调用`setState()` ，来避免不必要的重新渲染。

### forceUpdate()

```jsx
component.forceUpdate(callback)
```
按照默认，当你的component 的state 或props 反生改变时，你的component 将会被重新渲染。如果你的`render()` 方法依赖一些其他的数据，你可以告诉React，component 需要调用`foreceUpdate()` 来重新渲染。

调用`forceUpdate()` 将会导致component 的`render()` 被调用，跳过`shouldComponentUpdate()`。这将触发孩子component 的普通的lifecycle methods，包括每一个孩子的`shouldComponentUpdate()` 方法。如果这个标记改变React 将仍然只更新这个DOM。

通常你应该超时避免`forceUpdate()` 的所有使用，并且`render()` 方法中的`this.props` 和`this.state` 应该是只读的。

## Class Properties

### defaultProps

`defaultProps` 可以作为component 类的属性来定义，为类设置默认的props。这是为undefined 的props 使用，而不是为null props 使用。例如：
```jsx
class CustomButton extends Component {
  // ...
}

CustomButton.defaultProps = {
  color: 'blue'
}
```
如果`props.color` 没有被提供，它将默认被设置为`blue`：
```jsx
render(){
  return <CustomButton />;// props.color will be set to blue
}
```
如果`props.color` 被设置为null，它将仍然是null：
```jsx
render(){
  return <CustomButton color={null} />;// props.color will remain null
}
```

### displayName

`displayName` 字符串被用在调试信息中。JSX 自动设置这个值；参考[JSX in Depth](https://facebook.github.io/react/docs/jsx-in-depth.html)。

### propTypes

`propTypes` 可以作为component 类的属性来定义，去定义这个props 应该是什么类型。它应该是在[React.PropTypes](https://facebook.github.io/react/docs/react-api.html#react.proptypes) 中从prop 名称到类型的一个映射。在开发模式中，当一个无效的值提供给props，一个警告将会在JavaScript console 中被显示。在生产模式中，为了效率，`propsTypes` 的检测将会被跳过。

例如，下面的例子确保`color` 是一个字符串：
```jsx
class CustomButton extends Component {
  // ...
}

CustomButton.propTypes = {
  color: React.PropTypes.string
}
```
我们建议尽可能的使用[Flow](https://flowtype.org/)，去获取编译时（compile-time）类型检测去替代运行时（runtime）类型检测。[React 已经内置支持Flow](https://flowtype.org/docs/react.html)，所以它很容易在一个React app 中进行静态分析。

## Instance Properites

### props

`this.props` 包含compoent 被调用着的props。参考[Components and Props](https://facebook.github.io/react/docs/components-and-props.html)去了解props。

尤其，`this.props.children` 是个特殊的props，明显地被JSX 表达式中的孩子标签定义，而不是这个标签自身。

### state

component 包含特定于该compoent 的数据，可能随着时间而改变。state 是用户定义的，它应该是一个普通的（plain）JavaScript 对象。

如果你不在`render()` 中使用它，它就不应该是state。例如，你可以直接在实例上设置定时器ID。

参考[State and Lifecycle](https://facebook.github.io/react/docs/state-and-lifecycle.html) 去了解更多关于state 的信息。

永远不要直接改变`this.state`，通过调用`setState()` 来替代你做的这个改变。对待`this.state` 就像它是不会改变。
