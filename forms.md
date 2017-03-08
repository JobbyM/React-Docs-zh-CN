> 此文章是翻译[forms](https://facebook.github.io/react/docs/forms.html)这篇React（版本v15.4.0）官方文档。

## Form

在React 中，HTML form elements 同其它DOM elements 的使用几乎没有不同，以为你form elements 本身就保存一些内部state。例如，下面这个纯HTML form 接受一个name。
```html
<form>
  <label>
    Name:
    <input type="text" name="name"/>
  </label>
  <input type="submit" value="Submit"/>
</form>
```
当用户提交一个表单时，form 会有一个默认打开一个新页面的行为。在React 中如果你想要这个行为，它也工作。但是在大多数情况下，通过JavaScript function 去控制表单提交并获取用户在form 中的行为是很方便的。使用“controlled components” 这种技术来实现这种标准行为。

### Controlled Components

在HTML 中，像`<input>`、`<textarea>` 和`<select>` 这些form elements 通常保留它们自己的状态并根据用户输入来更新。在React 中，可变状态（mutable state）通常保存在组件的状态属性中（state property of components），并且只能由`setState()` 更新。

我们可以结合两方面来使React state 成为“single source of truth”。然后React 组件渲染form 同时也能控制后续的用户的输入。一个input form element 的值通过React 这种方式控制称为“controlled component”。

例如，如果我们想要在之前例子提交之前输出name，我们可以这样写一个form 作为controlled component：
```jsx
class NameForm extends Component {
  constructor(props){
    super(props)
    this.state = {
      value : ''
    }

    this.handleChange = this.handleChange.bind(this)
    this.handleSubmit = this.handleSubmit.bind(this)
  }

  handleChange(event){
    this.setState({
      value: event.target.value
    })
  }

  handleSubmit(event){
    alert('A name was submitted: ' + this.state.value)
    event.preventDefault()
  }

  render(){
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" name="name" value={this.state.value} onChange={this.handleChange}/>
        </label>
        <input type="submit" value="Submit"/>
      </form>
    )
  }
}
```
由于`value` 特性（attribute）设置在form element，将总是展示`tthis.state.value` 这个值，使React state 成为“the source of truth”。由于`handleChange` 总是随着每一次按键而运行去更新React state，展示的值也总是随着用户的键入而更新。

对于controlled component，每一次状态改变（state mutation）总是有相应的句柄函数（handler function）。这能更直接的修改和验证用户的输入。例如，如果我们想强制将每一个输入都变成大写，我们可以如下修改`handleChange`：
```jsx
  handleChange(event){
    this.setState({
      value: event.target.value.toUpperCase()
    })
  }
```

### The textarea Tag

在HTML 中，`<textarea>` element 通过子节点定义它的文字：
```html
<textarea>
  Hello there, this is some text in a text area
</textarea>
```
在React 中，`<textarea>` 通过使用`value` 特性（attribute）来替代。这种方式使用`<textarea>` 在写法上非常类似form 使用单行输入（single-line input）：
```jsx
class EssayForm extends Component {
  constructor(props){
    super(props)
    this.state = {
      value : 'Please write an essay about your favourite DOM element'
    }
    this.handleChange = this.handleChange.bind(this)
    this.handleSubmit = this.handleSubmit.bind(this)
  }

  handleChange(event){
    this.setState({
      value: event.target.value
    })
  }

  handleSubmit(event){
    alert('An essay was submitted: ' + this.state.value)
    event.preventDefault()
  }

  render(){
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit"/>
      </form>
    )
  }
}
```
注意`this.state.value` 是在构造函数（constructor）中被初始化，所以这个文本域（text area）是以这里的文本为默认值的。

### The select Tag

在HTML 中，`<select>` 创建一个下拉列表。例如，下面这个HTML 创建了一个鲜花的下拉列表：
```html
<select>
  <option value="grapefruit">Grapefruit</option>
  <option value="lime">Lime</option>
  <option value="coconut" selected>Coconut</option>
  <option value="mango">Mango</option>
</select>
```
注意到由于`selected` 特性（attibute），Coconut option 是初始化被选中的。在React 中通过在根`select` 标签上使用`value`特性（attribute）来替代使用`selected` 特性（attibute）。这对于controlled component 来说更方便，因为你只需要更新这一个位置就可以了。例如：
```jsx
class FlavorForm extends Component {
  constructor(props){
    super(props)
    this.state = {
      value: 'coconut'
    }

    this.handleChange = this.handleChange.bind(this)
    this.handleSubmit = this.handleSubmit.bind(this)
  }

  handleChange(event){
    this.setState({
      value: event.target.value
    })
  }

  handleSubmit(event){
    alert('Your favourite flavor is: ' + this.state.value)
  }

  render(){
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
    )
  }
}

```
总的来说，这使得`<input type="text>`、`<textarea>` `<select>`的使用非常相似-它们都可以通过接受一个`value` 来实现controlled component。

### Alternatives to Controlled Components

有时使用controlled component 是非常令人厌烦的，因为你必须写事件句柄（event handler）为每一次你的数据改变和通过React component 来传递输入状态（input state）。尤其是在你改变之前的代码到React 或者在非React（non-React）库中使用React。在这些情况下，你可能想要符合[uncontrolled components](https://facebook.github.io/react/docs/uncontrolled-components.html)，一个替代实现input form 的技术。
