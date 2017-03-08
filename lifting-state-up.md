> 此文章是翻译[lifting-state-up](https://facebook.github.io/react/docs/lifting-state-up.html)这篇React（版本v15.4.0）官方文档。

## Lifting State Up

通常，几个组件需要响应相同的数据变化。我们建议提升共享状态（lifting the shared state up）到距离它们最近的父组件。让我们看看这是如何运转的。

在这部分，我们将要创建一个温度计算器来计算给定的温度是否使水沸腾。

我们将创建一个`BoilingVerdict` 组件。它接受一个`celsius` 温度作为props，然后输出是否能够使水沸腾：
```jsx
function BoilingVerdict(props){
  if(props.celsius >= 100){
    return <p>The water would boil.</p>
  }else{
    return <p>The water would nott boil.</p>
  }
}
```

然后，我们会创建一个`Calculator` 组件。它渲染一个`<input>` 接受你的键入的温度（ temperature），并将此值保存在`this.state.value` 中。

另外，它会当前输入值渲染`BoilingVerdict`。
```jsx
class Calculator extends Component {
  constructor(props){
    super(props)
    this.state = {
      value: ''
    }
    this.handleChagne = this.handleChagne.bind(this)
  }

  handleChagne(event){
    this.setState({
      value: event.target.value
    })
  }

  render(){
    const value = this.state.value
    return (
      <fieldset>
        <legend>Enter temperature in celsius:</legend>
        <input
          value={value}
          onChange={this.handleChagne} />
        <BoilingVerdict celsius={parseFloat(value)} />
      </fieldset>
    )
  }
}
```

### Adding a Second Input

我们的新需求是，除了Celsius input ，我们还需要提供一个Fahrenheit input，并且让它们保持同步（in sync）。

开始我们可以从`Calculator` 组件中提取一个`TemperatureInput` 组件。我们将添加一个`scale` prop ，只可以接受“c”或“ｆ”：
```jsx
const scaleNames = {
  c: 'Celsius',
  f: 'Fahrenheit'
}

class TemperatureInput extends Component {
  constructor(props){
    super(props)
    this.state = {
       value: ''
     }
    this.handleChange = this.handleChange.bind(this)
  }

  handleChange(event){
    this.setState({
      value: event.target.value
    })
  }

  render(){
    const value = this.state.value
    const scale = this.props.scale
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}</legend>
        <input
          value={value}
          onChange={this.handleChange} />
      </fieldset>
    )
  }
}
```
现在我们可以修改`Calculator` 去渲染两个不同的temperature inputs：
```jsx
class Calculator extends Component {
  render(){
    return (
      <div>
        <TemperatureInput scale="c" />
        <TemperatureInput scale="f" />
      </div>
    )
  }
}
```
现在我们有两个input，但是当你在其中一个键入temperature时，另一个不会更新。这同我们的需求相反：我们想要它们保持同步（in sync）。

我们也不能在`Calculator` 中展示`BoilingVerdict`。这个`Calculator` 不知道当前的temperature 因为它被隐藏在`TemperatureInput` 中。

### Lifting State Up

首先，我们要写两个函数来互相转换`Celsisus` 和 `Fahrenheit`。
```jsx
function toCelsius(fahrenheit){
  return (fahrenheit - 32 ) * 5 / 9
}

function toFahrenheit(celsius){
  return (celsius * 9 / 5) + 32
}
```
这两个函数转换数值。我们还要写另一个函数，它接受一个string 类型的`value`和一个转换函数作为参数并且返回一个string。我们调用此函数来通过一个输入值获取另一个输入值。

如果是一个无效的`value` 它将返回一个空字符串，并且输出值保留三位小数。
```jsx
function tryCovert(value, convert){
  const input = parseFloat(value)
  if(Number.isNaN(input)){
    return ''
  }
  const output = convert(input)
  const rounded = Math.round(output * 1000) / 1000
  return rounded.toString()
}
```
例如，`tryConvert('abc', toCelsisu)` 将返回一个空字符串，而`tryConvert('10.22', toFahrenheit)` 将返回`50.369`。

下面，我们将从`TemperatureInput` 中移除state。

相反，它将会从props 中接受`value` 和`onChange` 句柄：
```jsx
class TemperatureInput extends Component {
  constructor(props){
    super(props)
    this.handleChange = this.handleChange.bind(this)
  }

  handleChange(event){
    this.props.onChange(event.target.value)
  }

  render(){
    const value = this.props.value
    const scale = this.props.scale
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}</legend>
        <input
          value={value}
          onChange={this.handleChange} />
      </fieldset>
    )
  }
}
```
如果几个组件需要访问相同的数据，一个标志就是state 应该被提升到它们相邻的最近父组件。在我们这个例子中，这个父组件是`Calculator`。我们将要存储当前的`value` 和`scale`在他的state 中。

实践证明，我们不需要存储两个inputs 值。我们只需要存储最近修改的input ，和它所代表的scale 就可以了。我们可以根据当前的`value` 和`scale` 推出另一个input 中的值。

因为是根据相同的state 来计算它们的值，所以inputs 可以保持同步。
```jsx
class Calculator extends Component {
  constructor(props){
    super(props)
    this.handleCelsiusChange = this.handleCelsiusChange.bind(this)
    this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this)
    this.state = {
      value: '',
      scale: 'c'
    }
  }

  handleCelsiusChange(value){
    this.setState({
      scale: 'c',
      value
    })
  }

  handleFahrenheitChange(value){
    this.setState({
      scale: 'f',
      value
    })
  }

  render(){
    const scale = this.state.scale
    const value = this.state.value
    const celsius = scale === 'f' ? tryCovert(value, toCelsius) : value
    const fahrenheit = scale === 'c' ? tryCovert(value, toFahrenheit) : value
    return (
      <div>
        <TemperatureInput
          scale="c"
          value={celsius}
          onChange={this.handleCelsiusChange}/>
        <TemperatureInput
          scale="f"
          value={fahrenheit}
          onChange={this.handleFahrenheitChange}/>
      </div>
    )
  }
}
```
现在，无论是你在那一个input 进行编辑，在`Calculator` 中`this.state.value` 和`this.state.scale` 都会更新。其中的一个input 获取value，任何input 都会被保存，另一个input 都会基于这个值来更新。

### Lessions Learned

在React 应用中任何数据变动都应该是基于一个"source of truth"。通常state 被首先加入组件去渲染。然后，如果另一个组件也需要它，那么你就可以将它们提升到它们的父组件中。而不是尝试在不同的组件中同步state，你应该依赖[top-down data flow](https://facebook.github.io/react/docs/state-and-lifecycle.html#the-data-flows-down)。

通过专注写模版代码（boilerplater）而不是进行双向绑定，但是有一个优点，它能花费很少工作去发现和隔离bug。因为任何state 都存在于组件而组件可以独立改变state，确定bug 的返回也会减少。除此之外，你可以实现自定义逻辑去拒绝或改变用户输入。

如果有些东西可以来自props 或者state ，那么它就不应该存在与state 中。例如，我们存储`value` 和`scale` 而不是存储`celsiusValue` 和`fahrenheitValue`。另一个输入值总是可以在`render()` 方法中被计算得到。这可以让我们清晰的 应用在任何取值范围而不会失去用户输入精度。

如果在UI 中有错误，你可以使用[React Developer Tools](https://github.com/facebook/react-devtools) 对props 进行检测，并将其移动到树上，知道找到负责更新的组件为止。这可以让你从源代码上跟踪bug。
