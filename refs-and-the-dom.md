> 此文章是翻译[Refs and the DOM](https://reactjs.org/docs/refs-and-the-dom.html)这篇React（版本v16.2.0）官方文档。

## Refs and the DOM

在典型的React 数据流中， [props](https://reactjs.org/docs/components-and-props.html) 是父组件同子节点进行交流的唯一方式。为了修改一个子节点，你需要用新的props 进行重新渲染。然而，还有几种情形需要你在典型的数据流之外进行命令式修改一个子节点。这个被修改的子节点可能是一个React 组件实例，也有可能是一个DOM 元素。对于上述两种情况，React 提供了一种应急方案（an escape hatch）.

### When to Use Refs

有一些很好的使用refs 的用例：

* 管理焦点，文本选择或媒体播放。
* 触发命令式动画。
* 与第三方DOM库集成。

可以声明式地做任何事情，避免使用refs 。

例如，不要在Dialog 组件上暴露`open()`和`close()`方法，而是将一个`isOpen` prop传递给它。

### Don't Overuse Refs

在你的app 中，你一开始倾向使用refs 是为“使事情发生（make things happen） ”。在这种情况下，花一点时间，更仔细地思考state 应该位于组件层级中的什么位置。通常，它会变得更清晰，当正确地放置state 在更高的层级中。参考[LIfting State Up](https://reactjs.org/docs/lifting-state-up.html) 这个例子的指南（guide）。

### Adding a Ref to a DOM Element

React 提供了一个特殊的特性，你可以将其添加到任何组件上。`ref` 特性接受一个回调函数，此回调函数在组件被立即执行，当组件被加载或卸载时。

当`ref` 特性被应用在一个HTML 元素上时，`ref` 回调函数将会接受底层的DOM 元素（underlying DOM element）作为它的参数。例如，下面代码使用ref 回调函数去存储一个DOM 节点的引用（a reference to a DOM node）：

```
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.focusTextInput = this.focusTextInput.bind(this);
  }

  focusTextInput() {
    // Explicityle focus the text input using the raw DOM API
    this.textInput.focus();
  }

  render() {
    //  Using the `ref` callback to store a reference to the text input DOM
    // element in an instance field (for example, this.textInput).
    return (
      <div>
        <input
          type="text"
          ref={(input) => { this.textInput = input; }}/>
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

当组件加载时，React 将会用DOM 元素调用`ref` 回调函数，当组件卸载时，将使用`null` 调用此回调函数。`ref` 回调函数在`componentDidMount` 或`componentDidUpdate` 生命周期钩子之前调用。

使用`ref` 回调函数去设置一个类的属性是访问DOM 元素一种常用方式。首选的方法是在`ref`回调中设置属性，就像上面的例子一样。 甚至有一个较短的写法：`ref = {input => this.textInput = input}`。

### Adding a Ref to a Class Component

当`ref` 特性用在自定义的类组件上时，这个`ref` 回调函数将会接受加载后的组件实例作为它的参数。例如，如果我们想要包裹上面的`CustomTextInput` ，在加载之后，去模拟立即被点击：

```
class AutoFocusTextInput extends React.Component {
  componentDidMount() {
    this.textInput.focusTextInput();
  }

  render(){
    return (
      <CustomTextInput
        ref={(input) => { this.textInput = input; }} />
    )
  }
}
```

注意只有`CustomTextInput` 被声明为一个类时才有效：

```
class CustomTextInput extends React.Component {
 //...
}
```

### Refs and Functional Components

**你不可以在函数式组件上使用`ref` 特性**，因为它们没有实例：

```
function MyFunctionalComponent() {
  return <input />;
}

class Parent extends React.Component {
  render() {
    // This will *not* work!
    return (
      <MyFunctionalComponent
        ref={(input) => { this.textInput = input; }} />
    );
  }
}
```

如果需要ref，你应该将组件转换为类，就像在需要生命周期方法或state时做的一样。

但是，你可以 **在函数式组件中的`ref`特性**，只要你引用一个DOM元素或类组件，：

```
function CustomTextInput(props) {
  // textInput must be declared here so the ref callback can refer to it
  let textInput = null;

  function handleClick() {
    textInput.focus();
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
  );
}
```

## Exposing DOM Refs to Parent Components

在少数情况下，你可能需要从父组件中访问孩子的DOM 节点。这通常不会被推荐，因为这破坏了组件的封装性，但是它偶尔是有用的，例如触发focus 或者测量孩子DOM 节点的大小和位置。

虽然你可以[add a ref to to the child component](https://reactjs.org/docs/refs-and-the-dom.html#adding-a-ref-to-a-class-component)，但是这不是一个理想的解决方案，因为你只是得到一个组件的实例而不是一个DOM 节点。除此之外，它也不会在函数式组件中工作。

相反，在这种情况下，我们建议在孩子上暴露特殊的prop。 孩子将使用任意名称的函数prop（例如`inputRef`），并将其作为`ref` 特性附加到DOM 节点上。 这允许父节点通过中间的组件将其ref 回调传递给孩子的DOM 节点。

这适用于类组件和函数式组件。

```
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
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

在上面的例子中，`Parent` 将它的ref 回调作为`inputRef` prop传递给`CustomTextInput`，`CustomTextInput` 将作为特殊的`ref` 特性相同的函数传递给`<input>`。因此，`Parent`中的`this.inputElement`将被设置为与`CustomTextInput`中的`<input>` 元素相对应的DOM 节点。

请注意，上述示例中的`inputRef` prop的名称没有特殊的含义，因为它是一个常规的组件 prop。然而，在`<input>` 本身上使用`ref` 特性很重要，因为它告诉React 将ref 附加到其DOM 节点。

即使`CustomTextInput`是一个函数式组件，这也是有效的。与[只能为DOM 元素和类组件指定的](https://reactjs.org/docs/refs-and-the-dom.html#refs-and-functional-components)特殊ref 特性不同，对于诸如`inputRef` 的常规组件 props没有限制。

这种模式的另一个好处就是它的对更深组件嵌套也有用。例如，假设`Parent` 不需要DOM 节点，但是渲染`Parent` 的组件（让我们称之为`Grandparent`）需要访问它。然后我们可以让`Grandparent` 指定对`Parent` 的`inputRef` prop，并让`Parent` “转发（forward）”到`CustomTextInput`：

```
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
      My input: <CustomTexInput inputRef={props.inputRef} />
    </div>
  );
}

class Grandparent extends React.Component {
  render() {
    return (
      <Parent
        inputRef={el => this.inputElement = el }
      />
    );
  }
}
```

在这里，ref 回调首先由`Grandparent` 指定。 它被传递给`Parent`作为一个常规的prop 称为`inputRef`，`Parent` 也将它传递给`CustomTextInput` 作为prop。 最后，`CustomTextInput`读取`inputRef` prop，并将传递的函数作为`ref` 特性附加到`<input>`。 因此，在`Grandparent`中的`this.inputElement` 将被设置为与`CustomTextInput` 中的`<input>` 元素相对应的DOM 节点。

所有的事情都考虑到了，我们建议尽可能不暴露DOM 节点，但这可以是一个有用的逃生舱（escape hatch）。 请注意，此方法要求你向子组件添加一些代码。 如果你无法绝对控制子组件实现，最后一个选项是使用[`findDOMNode()`](https://reactjs.org/docs/react-dom.html#finddomnode)，但不鼓励。

### Legacy API: String Refs

如果你之前使用过React，则可能熟悉一个较旧的API，其中`ref`属性是一个字符串，如`"textInput"`，DOM node作为`this.refs.textInput`访问。 我们建议不使用它，因为字符串refs有[一些问题](https://github.com/facebook/react/pull/8333#issuecomment-271648615)，被认为是历史遗留问题，并且 **可能会在以后的版本中被删除**。 如果你正在使用`this.refs.textInput`访问refs，则建议使用回调模式。

### Caveats

如果`ref`回调被定义为内联函数（inline function），则在更新期间将被调用两次，首先为`null`，然后再次使用DOM element。 这是因为使用每个渲染创建一个新的函数实例，所以React需要清除旧的ref并设置新的ref。 你可以通过将`ref`回调定义为类的绑定方法来避免这种情况，但请注意，在大多数情况下，这并不重要。
