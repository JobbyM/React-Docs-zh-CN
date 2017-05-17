> 此文章是翻译[Uncontrolled Components](https://facebook.github.io/react/docs/uncontrolled-components.html))这篇React（版本v15.5.4）官方文档。

## Uncontrolled Components

在大多数情况下，我们建议使用[controlled component](https://facebook.github.io/react/docs/forms.html) 去实现forms。在一个controlled component 中，form 数据被React component 控制。这个uncontrolled components 替代方案中，form 数据被DOM 自身控制。

写一个uncontrolled component 而不是为每一个state 更新写一个事件句柄（event handler），你可以[使用ref](https://facebook.github.io/react/docs/refs-and-the-dom.html) 从DOM 中获取值。

例如，下面代码是在一个uncontrolled component 中接受一个名字：
```jsx
class NameForm extends Component {
  constructor(props){
    super(props)
    this.handleSubmit = this.handleSubmit.bind(this)
  }

  handleSubmit(event){
    alert('A name was sumbitted:' + this.input.value)
    event.preventDefault()
  }

  render(){
    return (
      <form onSubmit={this.handleSubmit} >
        <label>
          <input
            type="text"
            ref={(input) => this.input = input} />
        </label>
        <input type="submit" value="submit" />
      </form>
    )
  }
}
```
[在CodePen 上尝试](https://codepen.io/gaearon/pen/WooRWa?editors=0010)

由于uncontrolled component 在DOM 中保存the source of truth，在使用uncontrolled components 中，合并React 和non-React 代码有时是比较容易。如果你想要快速和肮脏，你可以使用少量代码。然后，你通常使用的是controlled components。

如果在特殊场景下你仍然不清楚使用那种类型的component，这篇[this article on controlled versus uncontrolled inputs](http://goshakkk.name/controlled-vs-uncontrolled-inputs-react/)文章可能对你有帮助。

### Default Values

在React 渲染lifecycle 中，在form elements 上的`value` attribute 将会覆盖DOM 中的值。使用uncontrolled component，你经常需要使用React 去配置这个初始值，但是这里随后的更新是失去控制的。为了处理这种情况，你需要配置一个`defaultValue` attribute 而不是value。
```jsx
  render(){
    return (
      <form onSubmit={this.handleSubmit} >
        <label>
          <input
            defaultValue="Bob"
            type="text"
            ref={(input) => this.input = input} />
        </label>
        <input type="submit" value="submit" />
      </form>
    )
  }
```
同样地，`<input type="checkbox">` 和`<input type="radio">` 支持`defaultChecked` 而`<select>` 支持`defaultValue`。
