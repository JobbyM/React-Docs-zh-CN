> 此文章是翻译[Refs and the DOM](https://facebook.github.io/react/docs/refs-and-the-dom.html)这篇React（版本v15.5.4）官方文档。

## Refs and the DOM

在典型的React 数据流（dataflow）中， [props](https://facebook.github.io/react/docs/components-and-props.html) 是父component 同子节点进行交流的唯一方式。为了修改一个子节点，你需要用新的props 进行重新渲染。然而，还有几种情形需要你在典型的数据流之外进行命令式修改一个子节点。这个被修改的子节点可能是一个React component 实例，也有可能是一个DOM element。对于上述两种情况，React 提供了一种应急方案（an escape hatch）.

### When to Use Refs

有一些很好的使用refs 的用例：

* 管理焦点，文本选择或媒体播放。
* 触发强制性动画。
* 与第三方DOM库集成。

避免使用refs作为声明性地做任何事情。

例如，不要在Dialog组件上暴露`open()`和`close()`方法，而是将一个`isOpen` prop传递给它。

### Don't Overuse Refs

在你的app 中，你一开始倾向使用refs 是为“使事情发生（make things happen） ”。在这种情况下，花一点时间，更仔细地思考state 应该位于component 层级中的什么位置。通常，它会变得更清晰当正确地将state 放置在更高的component层级中。参考[LIfting State Up](https://facebook.github.io/react/docs/lifting-state-up.html) 这个例子的指南（guide）。

### Adding a Ref to a DOM Element

React 提供了一种特殊的attribute ，你可以将其添加到任何component 上。`ref` attribute 接受一个回调函数，此回调函数在component 被mounted 或unmounted 时被立即执行。

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

使用`ref` 回调函数去设置一个class 的属性（property）是访问DOM elements 一种常用方式。首选的方法是在`ref`回调中设置属性，就像上面的例子一样。 甚至有一个较短的写法：`ref = {input => this.textInput = input}`。

### Adding a Ref to a Class Component

当`ref` attribute 作为class 被用在自定义component 上时，这个`ref` 回调函数将会接受mounted 的component 实例作为它的参数。例如，如果我们想要在mouting 之后，模拟立即在`CustomTextInput` 上被点击：
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
注意只有`CustomTextInput` 被声明为一个class 时才有效：
```jsx
class AutoFocusTextInput extends Component {
 //...
}
```

### Refs and Functional Components

**你不可以在functional component 上使用`ref` attribute**，因为它们没有实例：
```jsx
function MyFunctionalComponent() {
  return <input />;
}

class Parent extends React.Component {
  render() {
    // This will *not* work!
    return (
      <MyFunctionalComponent
        ref={(input) => { this.textInput = input;}} />
    );
  }
}
```
如果需要ref，你应该将component 转换为class，就像在需要生命周期方法或state时一样。

但是，只要你引用DOM元素或class component，你可以 **使用functional component中的`ref`属性**：
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

## Exposing DOM Refs to Parent Components

在少数情况下，你可能需要从父component 中访问孩子的DOM node。这通常不会被推荐，因为这破坏了component 的封装型，但是它偶尔是有用的，例如触发focus 或者测量孩子DOM node 的大小为位置。

虽然你可以[add a ref to to the child component](https://facebook.github.io/react/docs/refs-and-the-dom.html#adding-a-ref-to-a-class-component)，但是这不是一个理想的解决方案，因为你只是得到一个component 的实例而不是一个DOM node。除此之外，它也不会在functional component 中工作。

相反，在这种情况下，我们建议在孩子上暴露特殊的prop。 孩子将使用任意名称的函数prop（例如`inputRef`），并将其作为`ref`属性附加到DOM节点。 这允许父节点通过中间的组件将其ref回调传递给孩子的DOM节点。

这适用于class和functional components。
```jsx
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  )
}


class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    );
  }
}
```

在上面的例子中，`Parent`将它的ref回调作为`inputRef` prop传递给`CustomTextInput`，`CustomTextInput`将与`ref`特性相同的函数传递给`<input>`。因此，`Parent`中的`this.inputElement`将被设置为与`CustomTextInput`中的`<input>`元素相对应的DOM node。

请注意，上述示例中的`inputRef` prop的名称没有特殊的含义，因为它是一个常规的component prop。然而，使用`<input>`本身的`ref`属性很重要，因为它告诉React将ref附加到其DOM node。

即使`CustomTextInput`是一个functional component，这也是有效的。与[只能为DOM elements和class components指定的](https://facebook.github.io/react/docs/refs-and-the-dom.html#refs-and-functional-components)特殊ref属性不同，对于诸如`inputRef`的常规component props没有限制。

这种模式的另一个好处就是它的对更深component 嵌套也有用。例如，假设`Parent`不需要DOM node，但是渲染为`Parent`的component（让我们称之为`Grandparent`）需要访问它。然后我们可以让`Grandparent`指定对`Parent`的`inputRef` prop，并让`Parent`“转发（forward）”到`CustomTextInput`：
```jsx
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

function Parent(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

class Grandparent extends React.Component {
  render() {
    return (
      <Parent
        inputRef={el => this.inputElement = el }
    )
    />
  }
}
```

在这里，ref回调首先由`Grandparent` 指定。 它被传递给`Parent`作为一个常规的prop称为`inputRef`，`Parent`将它传递给`CustomTextInput`作为prop。 最后，`CustomTextInput`读取`inputRef` prop，并将传递的函数作为`ref`属性附加到`<input>`。 因此，在`Grandparent`中的`this.inputElement`将被设置为与`CustomTextInput`中的`<input>`元素相对应的DOM node。

所有考虑的事情，我们建议尽可能不暴露DOM node，但这可以是一个有用的逃生舱（escape hatch）。 请注意，此方法要求你向child component添加一些代码。 如果你无法绝对控制child component实现，最后一个选项是使用[`findDOMNode()`](https://facebook.github.io/react/docs/react-dom.html#finddomnode)，但不鼓励。


### Legacy API: String Refs

如果你之前使用过React，则可能熟悉一个较旧的API，其中`ref`属性是一个字符串，如`"textInput"`，DOM node作为`this.refs.textInput`访问。 我们建议不使用它，因为字符串refs有[一些问题](https://github.com/facebook/react/pull/8333#issuecomment-271648615)，被认为是历史遗留问题，并且 **可能会在以后的版本中被删除**。 如果你正在使用`this.refs.textInput`访问refs，则建议使用回调模式。

### Caveats

如果`ref`回调被定义为内联函数（inline function），则在更新期间将被调用两次，首先为`null`，然后再次使用DOM element。 这是因为使用每个渲染创建一个新的函数实例，所以React需要清除旧的ref并设置新的ref。 你可以通过将`ref`回调定义为类的绑定方法来避免这种情况，但请注意，在大多数情况下，这并不重要。
