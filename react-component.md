> 此文章是翻译[React.Component](https://reactjs.org/docs/react-component.html)这篇React（版本v16.2.0）官方文档。

## React.Component

[组件](https://reactjs.org/docs/components-and-props.html)让你将UI 分成独立的、可复用的块，并且独立的思考每一块。`React.Component` 是由[React](https://reactjs.org/docs/react-component.html)提供。

### Overview

`React.Component` 是一个抽象的基类，所以它几乎很少去直接引用`React.Component`。相反，通常你会继承自它，并定义至少一个`render()` 方法。

通常你看可以使用纯[JavaScript class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 去定义一个React 组件：

```
class Greeting extends React.Component {
  render(){
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

如果你不曾使用ES6，你可以使用[`creat-react-class`](https://reactjs.org/docs/react-api.html#createclass)模块来代替。参考[Using React without ES6](https://reactjs.org/docs/react-without-es6.html) 去了解更多。

### The Component Lifecycle

每一个组件都有几个“lifecycle methods” 你可以在进程的特殊时间去覆盖运行代码。方法是 **will** 前缀的表示在某些事情发生前调用，方法是 **did** 前缀的表示在某些事情发生后调用。

#### Mounting

当组件的实例已经被创建并被插入到DOM 时被调用：

* [constructor()](https://reactjs.org/docs/react-component.html#constructor)
* [componentWillMount()](https://reactjs.org/docs/react-component.html#componentwillmount)
* [render()](https://reactjs.org/docs/react-component.html#render)
* [componentDidMount()](https://reactjs.org/docs/react-component.html#componentdidmount)

#### Updating

props 或state 的改变导致产生更新。当组件正在重新渲染时，下面方法被调用：

* [componentWillReceiveProps()](https://reactjs.org/docs/react-component.html#componentwillreceiveprops)
* [shouldComponentUpdate()](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)
* [componentWillUpdate()](https://reactjs.org/docs/react-component.html#componentwillupdate)
* [render()](https://reactjs.org/docs/react-component.html#render)
* [componentDidUpdate()](https://reactjs.org/docs/react-component.html#componentdidupdate)

#### Unmounting

当组件从DOM 中移除时这个方法被调用：

* [componentWillUnmount()](https://reactjs.org/docs/react-component.html#componentwillunmount)

#### Error Handing

当渲染过程中、生命周期方法中或任何子组件的构造函数中出现错误时，调用此方法。

* [ComponentDidCatch()](https://reactjs.org/docs/react-component.html#componentdidcatch)

### Other APIs

每一个组件也提供以下几个APIs：

* [setState()](https://reactjs.org/docs/react-component.html#setstate)
* [forceUpdate()](https://reactjs.org/docs/react-component.html#forceupdate)

### Class Properties

* [defaultProps](https://reactjs.org/docs/react-component.html#defaultprops)
* [displayName](https://reactjs.org/docs/react-component.html#displayname)

### Instance Properties

* [props](https://reactjs.org/docs/react-component.html#props)
* [state](https://reactjs.org/docs/react-component.html#state)

## 参考

### render()

```
render()
```

`render()` 方法是必须的。

当调用时，它应该检测`this.props` 和`this.state` 并返回一个下列类型之一：

* **React 元素**。 通常由JSX 创建。一个元素可以是一个原生的DOM 组件的表示（`<div />`），或用户定义的合成组件（`<MyComponent />`）。
* **字符串和数字**。这些作为DOM 中的文本节点被渲染。
* **Portals**。由[ReactDOM.createPortal](https://reactjs.org/docs/portals.html) 创建。
* **null**。渲染为空。
* **Booleans**。渲染为空。（主要存在于支`return test && <Child />` 模式，其中`test` 是布尔型）。

当返回`null` 或者是`false`, `ReactDOM.findDOMNode(this)` 将会返回`null`。

`render()` 方法应该是纯粹的，意味着它不会修改组件state，每次被调用都会返回相同的结果，并且它并不直接和浏览器交互。如果你需要同浏览器交互，在`componentDidMount()` 或其它生命周期方法中执行你的操作。 保持`render()` 使组件更容易去思考

>**注意**

>如果[`shouldComponentUpdate()`](https://reactjs.org/docs/react-component.html#shouldcomponentupdate) 方法返回false，`render()` 不会被调用。

#### Fragments

你可以从`render()` 中使用数组返回多个列表：

```
render() {
  return [
    <li key="A">First item</li>,
    <li key="B">Second item</li>,
    <li key="C">Third item</li>,
  ];
}
```

>**注意：**

>不要忘记在fragment 中[添加keys]() 到元素上去避免key 警告。


### constructor

```
constructor(props)
```

在它被加载前，React 组件的constructor 被调用。当实现继承自`React.Component` 的constructor 方法时，你应该在其它任何语句之前调用`super(props)`，否则，`this.props` 在constructor 中是undefined ，这将导致bug。

避免在构造函数中引入任何副作用或订阅。对于这些使用情况，使用`componentDidMount()` 替代。

在constructor 中正确初始化state。这样做，只赋值一个对象给`this.state`；不要传世在constructor 中调用`setState()`。constructor 中经常被用来绑定事件处理器到类实例上。

如果你不初始化state 也不绑定方法，你不必为你的React 组件去实现这个constructor。

在极少数情况下，可以根据props 来初始化state。 这样有效地“forks”props，并用最初的props 来设置state。这里有一个有效的`React.Component` 的子类的constructor的示例：

```
constructor(props){
  super(props);
  this.state = {
    color: props.initalColor
  };
}
```

谨防这种形式，因为state 将不会随着props 的更新而更新。 你通常不想将props与state 同步，而是要[提升状态（lift the state up）](https://reactjs.org/docs/lifting-state-up.html)。

如果你为了state 使用它们来“fork” props，你可以也想要实现[componentWillReceiveProps(nextProps)](https://reactjs.org/docs/react-component.html#componentwillreceiveprops) 去保持state 和它们同步更新。但是提升state 通常是更容易的并且很少产生错误。

### componentWillMount()

```
componentWillMount()
```

`componentWillMount()` 在加载发生之前被立即调用。它在`render()` 之前被调用，因此在这个方法中调用`setState()` 将不会触发重新渲染。通常，我们建议使用`constructor()`。

避免在这个方法中引入任何副作用或订阅。 对这些使用场景，使用`componentDidMount()` 替代。

这是在服务器渲染上调用的唯一生命周期钩子。

### componentDidMount()

```
componentDidMount()
```

`componentDidMount()` 在当组件被加载时被立即调用。初始化需要DOM 节点应该在这里了。如果你需要从远端（remote endpoint）去加载数据，这里实例化一个网络请求是正确的。在

此方法是设置订阅的好地方。如果你那样做，别忘了在`componentWillUnmount()`退订。

在这个方法中调用`setState()` 将不会触发重新渲染，，但它将在浏览器更新屏幕之前发生。这保证了即使在这种情况下，`render()` 将被调用两次，用户也不会看到中间状态。使用此模式时要谨慎，因为它经常会导致性能问题。但是，它可能是必要的，在渲染取决于DOM节点大小或位置的东西之前，你需要测量DOM 节点时，，比如modal和工具提示。

### componentWillReceiveProps()

```
componentWillRecevieProps(nextProps)
```

当已加载的组件接受新的props 之前`componentWillReceiveProps()` 方法被调用。如果你需要更新state 去响应prop 改变（例如，重置它），你可以对比`this.props` 和`nextProps` 并在这个方法中使用`this.setState()` 去执行state 改变。

注意当props 还没有发生改变，React 也可以调用这个方法， 所以如果你只像处理变化，请确保去比较当前值和下一个值。当父组件导致你的组件被重新渲染时，可能会发生这种状况。

在[加载](https://reactjs.org/docs/react-component.html#mounting)期间，React不使用初始props调用`componentWillReceiveProps`。 如果某些组件的props可能更新，它只会调用此方法。 调用`this.setState()` 通常不会触发`componentWillReceiveProps`。

### shouldComponentUpdate()

```
shouldComponentUpdate(nextProps, nextState)
```

使用`shouldComponentUpdate()` 让React 了解组件的输出是否不受当前state 或props 的改变影响。默认行为是在每一次state 改变时，都重新渲染，并且在大部分情况下，你应该依赖默认行为。

当新的props 或state 被接受时，`shouldComponentUpdate()` 在渲染之前被调用。默认是`true`。在一次渲染或`forceUpdate()` 被使用时，这个方法不被调用。

返回`false` 不能阻止子组件重新渲染，当它们的state 改变时。

当前，如果`shouldComponentUpdate()`  返回`false`，那么[componentWillUpdate()](https://reactjs.org/docs/react-component.html#componentwillupdate) ，[render()](https:///reactjs.org/react/docs/react-component.html#render) 和[componentDidUpdate()](https:///reactjs.org/react/docs/react-component.html#componentdidupdate) 都不会被调用。注意在未来React 可能将`shouldComponentUpdate()`作为一个提示而不是一个严格的指令，返回`false` 仍将导致组件重新渲染。

如果你在分析后确定特定的组件是慢的，你可以改变它去继承[React.PureComponent](https://reactjs.org/react/docs/react-api.html#react.purecomponent)，而它实现了一个浅prop和state 比较的`shouldComponentUpdate()`方法。 如果你有信心去亲自实现它，你可以比较`this.props` 和`nextProps` 以及`this.state` 和`nextState` 并返回`false` 告诉React 这次更新可以被跳过。

我们不推荐在`shouldComponentUpdate()` 中进行深度相等性检查，或者使用`JSON.stringify()`。它是非常低效的并将损害性能。

### componentWillUpdate()

```
componentWillUpdate(nextProps, nextState)
```

当新的props 或state 正在被接受，`componentWillUpdate()` 在渲染之前将立即被调用。在更新发生之前使用这个方法作为一个机会去执行这个准备。这个方法在第一次渲染时不会被调用。

注意你不能在这里调用`this.setState()`；你也不应该做其他任何事（例如，分发一个Redux action）会触发一个React 组件的更新，在`componentWillUpdate()` 返回之前。

如果你需要更新`state` 去响应`props` 改变，使用`componentWillReceiveProps()` 来代替。

>**注意**

>如果[shouldComponentUpdate()](https://reactjs.org/docs/react-component.html#shouldcomponentupdate) 返回false，`componentWillUpdate()` 将不会被调用。

### componentDidUpdate()

```
componentDidUpdate(prevProps, prevState)
```

当更新反生时`componentDidUpdate()` 立即被调用。这个方法在第一次渲染时不会被调用。

当组件已经被更新时，使用这个方法有机会去操作DOM。这也是一个好地方去做网络请求只要你对比当前props 和之前props（例如，如果props 还没有发生改变，网络请求可能不是必须的）。

>**注意**

>如果[shouldComponentUpdate()](https://reactjs.org/docs/react-component.html#shouldcomponentupdate) 返回false，`componentDidUpdate()` 将不会被调用。

### componentWillUnmount()

```
componentWillUnmount()
```

在组件被卸载和销毁之前，`componentWillUnmount()`立即被调用。在这个方法中执行必须的清理，例如，使定时器无效，取消网络请求或清除任何订阅，在`componentDidMount()` 中创建的。

### componentDidCatch()

```
componentDidCatch(error, info)
```

错误边界（Error boundaries）是React 组件捕获JavaScript 错误在它们子组件数组中的任何地方，记录这些些错误，展示一个回退UI（fallback UI）替代组件树的崩溃。错误边界捕获错误，在渲染中，在生命周期方法中，在构造函数中，在它们之下的整个树。

如果定义了这个生命周期方法，类组件就变成了错误边界。在里面调用`setState()`可以让你捕捉到下面的树中未处理的JavaScript 错误并展示回退UI。只使用错误边界来从意外异常中恢复；不要试图将它们用于控制流。

更多细节，查看[React 16 中错误处理](https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html)。

>**注意**

>错误边界只能捕获位于它们 **之下** 的组件书中的错误。一个错误边界不能捕获它自身的错误。

### setState()

```
setState(nextState, [callback])
```

`setState()` 将对组件的state 的更改排队，并通知React，该组件及其孩子需要使用更新的state 去重新渲染。这是用于响应事件处理程序和服务器响应更新用户界面的主要方法。

将`setState()` 作为请求而不是立即命令来更新组件。为了更好的感知性能，React 可能会延迟它，然后在单次中更新多个组件。 React 不能保证state 更改会立即生效。

`setState()` 并不总是立即更新组件。它可能会批量或延迟更新。这使得在调用`setState()` 之后立即读取`this.state` 存在一个潜在的陷阱。相反，使用`componentDidUpdate` 或`setState` 回调（`setState(updater，callback)`），其中任何一个在应用更新后都能保证触发。如果需要根据先前的state 设置state，请阅读下面的`updater`参数。

`setState()` 将永远导致重新渲染，除非`shouldComponentUpdate()` 返回`false`。如果正在使用可变对象，并且无法在`shouldComponentUpdate()` 中实现条件渲染逻辑，则仅当新state 与先前state 不同时才调用`setState()` 将避免不必要的重新渲染。

作为第一个参数，`updater` 的函数的签名：

```
(prevState, props) => stateChange
```

`prevState`是对以前state 的引用。 不应该直接改变。 相反，应该根据来自`prevState` 和`props` 的输入构建一个新对象来表示更改。 例如，假设我们想通过`props.step` 来增加state值：

```
this.setState((prevState, props) => {
  return {counter: prevState.counter + props.step};
})
```

`updater` 函数保证接收到的`prevState` 和`props` 是最新的。 updater的输出与`prevState`进行了浅层的合并（shallowly merged）。

`setState()`的第二个参数是一个可选的回调函数，它将在`setState` 完成并且重新渲染该组件后执行。 通常我们建议使用`componentDidUpdate()` 实现这样的逻辑。

你可以选择将一个对象作为第一个参数传递给`setState()` 来替代函数：

```
setState(stateChange, [callback])
```

这将执行`stateChange`的浅合并到新的state，例如调整购物车物品数量：

```
setState({quantity: 2})
```

这种形式的`setState()`也是异步的，并且在同一周期（cycle）内的多个调用可以进行批处理。 例如，如果你尝试在同一周期内多次增加项目数量，则将导致相当于：

```
Object.assign(
  previousState,
  {quantity: state.quantity + 1},
  {quantity: state.quantity + 1},
  ...
)
```

随后的调用将覆盖同一周期中先前调用的值，因此数量只会增加一次。 如果下一个状态取决于以前的状态，我们建议使用updater函数形式：

```
this.setState((prevState) => {
  return {counter: prevState.quantity + 1}
})
```

更多信息，请看[State and Lifecycle guide](https://reactjs.org/docs/state-and-lifecycle.html)

### forceUpdate()

```
component.forceUpdate(callback)
```

按照默认，当你的组件的state 或props 发生改变时，你的组件将会被重新渲染。如果你的`render()` 方法依赖一些其他的数据，你可以告诉React，组件需要调用`foreceUpdate()` 来重新渲染。

调用`forceUpdate()` 将会导致组件的`render()` 被调用，并且跳过`shouldComponentUpdate()`。这将触发子组件的普通的生命周期方法，包括每一个孩子的`shouldComponentUpdate()` 方法。如果这个标记改变React 将仍然只更新这个DOM。

通常你应该尽量避免`forceUpdate()` 的所有使用，并且`render()` 方法中的`this.props` 和`this.state` 应该是只读的。

## Class Properties

### defaultProps

`defaultProps` 可以作为类组件自身的属性来定义，为类设置默认的props。这是为undefined 的props 使用，而不是为null props 使用。例如：

```
class CustomButton extends React.Component {
  // ...
}

CustomButton.defaultProps = {
  color: 'blue'
}
```

如果`props.color` 没有被提供，它将默认被设置为`blue`：

```
render(){
  return <CustomButton />;// props.color will be set to blue
}
```

如果`props.color` 被设置为null，它将仍然是null：

```
render(){
  return <CustomButton color={null} />;// props.color will remain null
}
```

### displayName

`displayName` 字符串被用在调试信息中。通常，你不需要显式地设置它，因为它是从定义组件的函数或类的名称中推断出来的。你可能想要显式地设置它，如果你想为调试目的显示其他名称，或者在创建高阶组件时，参考[Wrap the Display Name for Easy Debugging](https://reactjs.org/docs/higher-order-components.html#convention-wrap-the-display-name-for-easy-debugging)。

## Instance Properites

### props

`this.props` 包含该组件调用者的props。参考[Components and Props](https://reactjs.org/docs/components-and-props.html)去了解props。

尤其，`this.props.children` 是个特殊的props，通常由JSX 表达式中的子标签定义，而不是标签自身。

### state

state 包含特定于该组件的数据，可能随着时间而改变。state 是用户定义的，它应该是一个普通的（plain）JavaScript 对象。

如果你不在`render()` 中使用它，它就不应该是state。例如，你可以直接在实例上设置定时器ID。

参考[State and Lifecycle](https://reactjs.org/docs/state-and-lifecycle.html) 去了解更多关于state 的信息。

永远不要直接改变`this.state`，因为调用`setState()` 之后，可能会替换你做的这个改变。对待`this.state` 就像它是不可变的。
