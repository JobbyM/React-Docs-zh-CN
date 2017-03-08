> 此文章是翻译[Refs and the DOM](https://facebook.github.io/react/docs/refs-and-the-dom.html)这篇React（版本v15.4.0）官方文档。

## Refs and the DOM

在典型的React 数据流（dataflow）中， [props](https://facebook.github.io/react/docs/components-and-props.html) 是父component 同子节点进行交流的唯一方式。为了修改一个子节点，你需要用新的props 进行重新渲染。然而，还有集中需要你在典型的数据流之外进行命令式修改一个子节点。这个被修改的子节点可能是一个React comoponent 实例，也有可能是一个DOM element。对于上述两种情况，React 提供了一种应急方案（an escape hatch）.

### The ref Callback Attribute

React 提供了一种特殊的attribute ，你可以将其添加到任何component 上。`ref` attribute 接受一个回调函数，此回调函数在component 被mounted 或unmounted 时被理解执行。

当`ref` attribute 被应用在一个HTML element 上时，`ref` 回调函数将会接受底层的DOM element（underlying DOM element）作为它的参数。例如，下面代码使用ref 回调函数去存储一个DOM 节点的引用（a reference to a DOM node）：
```jsx
class CustomTextInput extends Component {
  constructor(props){
    super(props)
    this.focus = this.focus.bind(this)
  }

  focus(){
    // Explicityle focus the text input using the raw DOM API
    this.textInput.focus()
  }

  render(){
    //  Using the `ref` callback to store a reference to the text input DOM
    // element in this.textInput
    return (
      <div>
        <input
          type="text"
          ref={(input) => {this.textInput = input;}}/>
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focus}
        />
      </div>
    )
  }
}
```
当component mounts 时，React 将会用DOM element调用ref 回调函数，当component unmounts 时，将使用`null` 调用此回调函数。

使用`ref` 回调函数去设置一个class 的属性（property）是访问DOM elements 一种常用方式。如果你现在在使用`this.refs.myRefName` 方法`refs`，我们建议使用这种方式来替换。

当`ref` attribute 被用在自定义component 上时，这个`ref` 回调函数将会接受mounted 的component 实例作为它的参数。例如，如果我们想要在mouting 之后，立即模拟在`CustomTextInput` 上被点击：
```jsx
class AutoFocusTextInput extends Component {
  componentDidMount(){
    this.textInput.focus()
  }

  render(){
    return (
      <CustomTextInput
        ref={(input)=>{this.textInput = input}} />
    )
  }
}
```
你不可以在functional component 上使用`ref` attribute ，因为它们没有实例。然而，你可以在一个functional component 内部的`render` 方法中使用`ref` attribute ：
```jsx
function CustomTextInput(props){
  // textInput must be declared here so the ref callback can refer to it
  let textInput = null

  function handleClick(){
    textInput.focus()
  }

  return (
    <div>
      <input
        type="text"
        ref={(input) => { textInput = input}} />
      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  )
}
```

### Don't Overuse Refs

在你的app 中，你一开始倾向使用refs 是为“使事情发生（make things happen） ”。在这种情况下，花一点时间，更仔细地思考state 应该位于component 层级中的什么位置。通常，它会变得更清晰当正确地将state 放置在更高的component层级中。参考[LIfting State Up](https://facebook.github.io/react/docs/lifting-state-up.html) 这个例子的指南（guide）。
