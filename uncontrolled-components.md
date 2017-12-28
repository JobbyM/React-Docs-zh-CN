> 此文章是翻译[Uncontrolled Components](https://reactjs.org/docs/uncontrolled-components.html)这篇React（版本v16.2.0）官方文档。

## Uncontrolled Components

在大多数情况下，我们建议使用[可控的组件](https://reactjs.org/docs/forms.html) 去实现表单。在可控的组件中，表单数据被React 组件控制。在这个不可控的组件替代方案中，表单数据被DOM 自身控制。

写一个不可控的组件，而不是为每一个state 更新写一个事件处理器，你可以[使用ref](https://reactjs.org/docs/refs-and-the-dom.html) 从DOM 中获取表单值。

例如，下面代码是在一个不可控的组件中接受一个名字：

```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleSubmit(event) {
    alert('A name was sumbitted:' + this.input.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit} >
        <label>
          <input type="text" ref={(input) => this.input = input} />
        </label>
        <input type="submit" value="submit" />
      </form>
    );
  }
}
```

[在CodePen 上尝试](https://codepen.io/gaearon/pen/WooRWa?editors=0010)

由于不可控的组件在DOM 中保存the source of truth，在使用不可控的组件时，集成React 和non-React 代码有时是比较容易。如果你想要快速和肮脏，你可以使用少量代码。否则，你应该通常使用的是可控的组件。

如果在特殊场景下你仍然不清楚使用那种类型的组件，这篇[this article on controlled versus uncontrolled inputs](http://goshakkk.name/controlled-vs-uncontrolled-inputs-react/)文章可能对你有帮助。

### Default Values

在React 渲染生命周期中，在表单元素上的`value` 特性将会覆盖DOM 中的值。使用不可控的组件，你经常需要使用React 去配置这个初始值，但是这里随后的更新是失去控制的。为了处理这种情况，你需要配置一个`defaultValue` 特性而不是`value`。

```
  render() {
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
    );
  }
```

同样地，`<input type="checkbox">` 和`<input type="radio">` 支持`defaultChecked` 而`<select>` 和`<textarea>` 支持`defaultValue`。
