> 此文章是翻译[forms](https://reactjs.org/docs/forms.html)这篇React（版本v16.2.0）官方文档。

## Form

在React 中，HTML 表单元素同其它DOM 元素的使用几乎没有不同，因为表单元素本身就保存一些内部状态。例如，下面这个纯HTML 表单接受一个name。

```
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

当用户提交一个表单时，表单会有会有一个默认打开一个新页面的HTML 表单行为。在React 中如果你想要这个行为，它也工作。但是在大多数情况下，通过JavaScript 函数去控制表单提交并获取用户在表单中的数据是很方便的。使用“可控的组件（controlled components）” 这种技术来实现这种标准行为。

### Controlled Components

在HTML 中，像`<input>`、`<textarea>` 和`<select>` 这些表单元素通常保留它们自己的状态并根据用户输入来更新。在React 中，可变状态（mutable state）通常保存在组件的状态属性中（state property of components），并且只能由[setState()](https://reactjs.org/docs/react-component.html#setstate) 更新。

我们可以结合这两方面，通过使React state 成为“单一数据源（single source of truth）”。然后React 组件渲染form 同时也能控制后续的用户的输入。一个输入表单元素的值通过React 这种方式控制称为“可控的组件”。

例如，如果我们想要在之前例子在提交时输出name，我们可以这样写一个表单作为可控的组件：

```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value : ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" name="name" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    )
  }
}
```

[在CodePen 上尝试](https://codepen.io/gaearon/pen/VmmPgp?editors=0010)

由于`value` 特性设置在表单元素，将总是展示`this.state.value` 这个值，使React state 成为单一数据源。由于`handleChange` 总是随着每一次按键而运行去更新React state，展示的值也总是随着用户的键入而更新。

对于可控的组件，每一次状态改变（state mutation）总是有相应的句柄函数。这能更直接的修改和验证用户的输入。例如，如果我们想强制将每一个输入都变成大写，我们可以如下修改`handleChange`：

```
  handleChange(event) {
    this.setState({value: event.target.value.toUpperCase()});
  }
```

### The textarea Tag

在HTML 中，`<textarea>` 元素通过子节点定义它的文字：

```
<textarea>
  Hello there, this is some text in a text area
</textarea>
```

在React 中，`<textarea>` 通过使用`value` 特性来替代。这种方式使用`<textarea>` 在写法上非常类似表单使用单行输入（single-line input）：

```
class EssayForm extends React.Component {
  constructor(props){
    super(props);
    this.state = {
      value : 'Please write an essay about your favourite DOM element'
    };
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('An essay was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Essay:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit"/>
      </form>
    )
  }
}
```

注意`this.state.value` 是在构造函数中被初始化，所以这个文本域（text area）是以这里的文本为默认值的。

### The select Tag

在HTML 中，`<select>` 创建一个下拉列表。例如，下面这个HTML 创建了一个鲜花的下拉列表：

```
<select>
  <option value="grapefruit">Grapefruit</option>
  <option value="lime">Lime</option>
  <option selected value="coconut">Coconut</option>
  <option value="mango">Mango</option>
</select>
```

注意到由于`selected` 特性，Coconut option 是初始化被选中的。在React 中通过在根`select` 标签上使用`value`特性来替代使用`selected` 特性。这对于可控的组件来说更方便，因为你只需要更新这一个位置就可以了。例如：

```
class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'coconut'};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Your favourite flavor is: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit} >
        <label>
          Pick your favourite La Croix flavor
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">Grapefruit</option>
            <option value="lime">Lime</option>
            <option value="coconut">Coconut</option>
            <option value="mango">Mango</option>
          </select>
        </label>
        <input type="submit" value="Submit"/>
      </form>
    );
  }
}
```

[在CodePen 上尝试](https://codepen.io/gaearon/pen/JbbEzX?editors=0010)

总的来说，这使得`<input type="text>`、`<textarea>` `<select>`的使用非常相似-它们都可以通过接受一个`value` 来实现可控的组件。

>注意

>你可以传递一个数组给`value` 特性，允许你在一个`select` 标签中选中多个options：

>```
<select multiple={true} value={['B', 'C']}
```

### Handling Mutiple Inputs

当你需要处理多个可控的 `input` 元素，你可以添加一个`name` 特性到每一个元素，并且让句柄函数根据`event.target.name` 的值去选择做什么。

例如：

```
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target
    const value = target.type === 'checkbox' ? target.checked : target.value
    const name = target.name

    this.setState({
      [name]: value
    })
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

[在CodePen 上尝试](https://codepen.io/gaearon/pen/wgedvV?editors=0010)

注意我们如何使用ES6[computed property name](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)语法更新state key 响应给定的input name:

```
this.setState({
  [name]: value
});
```

它同ES5 代码相同:

```
var partialState = {}
partialState[name] = value
this.setState(partialState)
```

此外，由于`setState()`自动将[部分状态合并到当前状态](https://reactjs.org/docs/state-and-lifecycle.html#state-updates-are-merged)，所以我们只需要调用更改的部分。

### Controlled Input Null Value

在可控的组件上指定值prop 可以防止用户更改输入，除非你希望这样做。如果你指定了一个值，但输入仍然是可编辑的，则可能意外地将`value`设置为`undefined` 或`null`。

下面的代码演示了这一点。(输入一开始是锁定的，但在短时间延迟后变得可编辑。)

```
ReactDOM.render(<input value="hi" />, mountNode);

setTimeout(function() {
  ReactDOM.render(<input value={null} />, mountNode);
}, 1000);
```

### Alternatives to Controlled Components

有时使用可控的组件是非常令人厌烦的，因为你必须写事件句柄为每一次你的数据改变和通过React 组件来传递输入状态（input state）。尤其是在你改变之前的React 代码或者使用非Reac 库集成到React 中。在这些情况下，你可能想要看看[uncontrolled components](https://reactjs.org/docs/uncontrolled-components.html)，一个替代技术实现输入表单的技术。
