> 此文章是翻译[Optimizing Performance](https://facebook.github.io/react/docs/optimizing-performance.html)这篇React（版本v15.4.0）官方文档。

## Optimizing Performance

在内部，React 使用一些聪明的技术去减少更新UI 操作所需要花费的昂贵的DOM 操作数量。对于许多应用，使用React 产生一个开始的用户操作而不需要做大量的专门优化性能的操作。不过，这里仍有集中方式去加速你的应用。

### Use The Production Build

如果你正在你的React apps 中检测或测试性能问题，请确保你使用最小生产环境（minified production）构建测试：
* 对于Create React App，你需要按照说明运行`npm run build`。
* 对于单个文件构建（single-file builds），我们提供为生产准备（production-ready） 的`.min.js` 版本。
* 对于Browserify，你需要使用`NODE_ENV=production` 去运行。
* 对于Webpack，你需要在你的生产环境配置进行添加如下插件：

```jsx
new webpack.DefinePlugin({
  'process.env': {
    NODE_ENV: JSON.stringify('production')
  }
}),
new webpack.optimize.UglifyJsPlugin()
```

在开发环境构建你的app 时，额外的警告会有用，但是由于额外的记账会变慢。

### Avoid Reconciliation

React 构建保持一个内部的渲染UI 的展现。它包括你从component 中返回的React elements。这种表现会让React 避免创建DOM 节点以及访问除必要之外的已经存在的节点，这会使得比在JavaScript 对象上的操作慢。有时它引用一个虚拟DOM（virtual DOM），但是它的工作方式和React Native 一致。

当一个component 的props 和state 反生改变，React 通过对比最新返回的element 同之前已经渲染的element 进行对比来决定师傅更新一个真实的DOM。当它们不想等，React 将会更新这个DOM。

在一些情况下，你的component 会加速这一切通过重写lifecycle function `shouldComponentUpdate` ，此函数在重新渲染过程之前触发。这个函数的默认实现是返回`true`,这让React 去执行这个更新：
```jsx
shouldComponentUpdate(nextProps, nextState){
  return true
}
```
如果你知道在某些情况下你的component 不需要更新，你可以通过在`shouldComponentUpdate` 中返回`false` 来跳过整个更新进程，包括调用component 上的`render()` 方法。

### shouldComponentUpdate In Action

这有一个component 的子树。对于每一个节点，`SCU` 表示`shouldComponentUpdate` 返回值，`vDOMEq` 表示这个渲染的React element 是否等价。最后，这个圆圈的颜色表示这个component 是否已经使一致。
![should-component-update](img/should-component-update.png)
由于`shouldComponentUpdate` 返回`false` 在子树的C2 处，React 不必渲染C2，也就不需要在C4 和C5 处调用`shouldComponentUpdate`。

在C1 和C3 处，`shouldComponentUpdate` 返回`true`，所以React 继续向下叶子节点并检测它们。在C6 处，`shouldComponentUpdate` 返回`true`，由于和已经渲染的element 不想等所以React 必须更新DOM。

最有趣的案例是C8。React 必须去渲染这个component，但是由于它返回的React element 同之前的已经渲染的element 相等，所以它不必去更新这个DOM。

注意，React 必须为C6 做DOM 改变，这是不可避免的。在C8 处，和已经渲染的React elements 摆脱了困境，对于C2 的子树和C7，它甚至不需要比较我们已经接受了`shouldComponentUpdate` 的elements，这个`render` 也不需要调用。

### Examples

当`props.color` 或者`state.count` 变量改变时，你的component 是否需要改变，应该通过这个`shouldComponentUpdate` 去检测：
```jsx
class CounterButton extends Component {
  constructor(props){
    super(props)
    this.state = {
      count: 1
    }
  }

  shouldComponentUpdate(nextProps, nextState){
    if(this.props.color !== nextProps.color){
      return true
    }
    if(this.state.count !== nextState.count){
      return true
    }
    return false
  }

  render(){
    return (
      <button
        color={this.props.color}
        onClick={() => this.setState(state => ({count: state.count + 1}))}>
        Count : {this.state.count}
      </button>
    )
  }
}
```
在上面代码中，`shouldComponentUpdate` 只是检测`props.color` 或者`state.count` 是否有任何改变。如果这些值没有改变，那么这个component 不需要更新。如果你的component 变得更复杂了，你需要使用一种类似的方法"shallow comparison" 在`props` 和`state` 域中去决定这个component 是否应该更新。这个方式是足够通用的并且React 提供了一个helper 去使用这个逻辑-只需要继承`React.PureComponent`。所以下面的代码去实现相同的功能是非常简单的：
```jsx
class CounterButton extends React.PureComponent {
  constructor(props){
    super(props)
    this.state = {
      count : 1
    }
  }

  render(){
    return (
      <button
        color={this.props.color}
        onClick={() => this.setState(state => ({count: state.count + 1}))}>
        Count : {this.state.count}
      </button>
    )
  }
}
```
大多数时间，你可以使用`React.PureComponent` 去替代你写`shouldComponentUpdate`。它只能做一个浅比较（shallow comparison），所以你不能使用它，当props 和state 在发生突变时，由于浅比较会错过。

对于更加复杂的数据结构这是一个问题。例如，我们说你想要`ListOfWords` component 去渲染一个分号分割（comma-separated ）的单词列表，它的父component 让你点击一个按钮去添加一个单词到列表中。下面代码不能正确运行：
```jsx
class ListOfWords extends React.PureComponent {
  render(){
    return <div>{this.props.words.join(',')}</div>
  }
}

class WordAdder extends Component {
  constructor(props){
    super(props)
    this.state = {
      words: ['marklar']
    }
    this.handleClick = this.handleClick.bind(this)
  }

  handleClick(){
    // This section is bad style and causes a bug
    const words = this.state.words
    words.push('marklar')
    this.setState({
          words: words
    })
  }
  render(){
    return (
      <div>
        <button onClick={this.handleClick} >Click Me</button>
        <ListOfWords words={this.state.words}/>
      </div>
    )
  }
}
```
问题是`PureComponent` 将会做一个简单的对比在`this.props.words` 的旧值和新值之间。由于这块代码在`WorldAddr` 中的`handleClick` 方法中改变了这个`words` 数组，这个`this.props.word` 的旧值和新值是相等的，即使是这个words 已经改变了。这个`ListOfWorlds` 不会更新即使它有了一个新值应该去更新。

### The Power Of Not Mutating DAta

避免这个问题最简单的方法是当你使用props 和state 时不去改变值。例如，上面的例子可以使用`concat` 去重写：
```jsx
  handleClick() {
    this.setState(prevState => ({
      words: prevState.words.concat(['marklar'])
    }));
  }
```
ES6 支持的数组的[spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator) 是其更简单。如果你his用Create React App，它已经默认支持了。
```jsx
  handleClick() {
    this.setState(prevState => ({
      words: [...prevState.words, 'marklar']
    }));
  }
```
按同样的方式，你可以重新这个可改变对象去避免改变。例如，我们说我们有一个`colormap` 的对象，我们写一个函数去改变`colormap.right` 为`blue`。我们可以这样写：
```jsx
function updateColorMap(colormap){
  colormap.right = 'blue'
}
```
写一个代码如果不改变原始对象，我们可以使用[Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 方法：
```jsx
function updateColorMap(colormap){
  return Object.assing({}, colormap, {right: 'blue'})
}
```
`updateColorMap` 现在返回一个新对象而不是改变的就对象。`Object.assign` 是ES6 方法需要使用一个 polyfill。

这有一个添加[object spread properties]()的JavaScript 提案（proposal），使得不需要改变就能简单更新对象：
```jsx
function updateColorMap(colormap){
  return {...colormap, right: 'blue'}
}
```
如果你使用Create React App，使用`Object.assign` 和`the object spread syntax` 默认是等价的。

### Using Immutable Data Structures

[Immutable.js](https://github.com/facebook/immutable-js) 是另一种解决这个问题的方法。它同结构共享提供不可变的、持久的集合来工作：

	* Immutable：一旦创建，一个集合在另一个时间点不能被改变。
	* Persistent：新集合可以从之前集合或一个可变的set 中创建。当新集合被创建之后原始的集合仍然有效。
	* Structral Sharing：创建的新集合尽可能和使用同原始集合一样的结构，减少复制量提示性能。


不变性（immutability）使得追踪改变非常简单。改变总是产生一个新对象所以我们只需要检测这个对象的引用是否改变。例如，下面常规的JavaScript 代码：
```jsx
const x = { foo: "bar"}
const y = x
y.foo = "baz"
x === y // true
```
虽然y 已经被编辑了，由于它和x 是同一个引用，这个比较返回`true`。你可以使用immutable.js 写类似的代码：
```jsx
const SomeRecord = Immutable.Record({ foo: null })
const x = new SomeRecord({ foo: 'bar' })
const y = x.set('foo', 'baz')
x === y // false
```
在这个例子中，当改变`x` 时返回一个新的引用，我们可以安全的假设`x` 已经改变了。

[seamless-immutable](https://github.com/rtfeldman/seamless-immutable)和[immutability-helper](https://github.com/kolodny/immutability-helper) 这两个库也可以帮助使用不可变数据。

不可变数据结构提供给你一个简单的方式追踪一个对象的改变，那是需要我们去实现`shouldComponentUpdate` 。它可以提供给你一个非常好的性能提升。
