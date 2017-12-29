> 此文章是翻译[React Without JSX](https://reactjs.org/docs/react-without-jsx.html)这篇React（版本v16.2.0）官方文档。

## React Without JSX

JSX 对于使用React 不是必须的。当你不想在你的构建环境中设置编译，使用不带JSX 的React 是非常方便的。

每一个JSX 元素只是调用`React.createElement(component, props, ...children)` 的语法糖（syntactic sugar）。所以，任何你使用JSX 可以做的事情都可以使用纯JavaScript 来实现。

例如，下面代码使用JSX ：

```
class Hello extends React.Component {
  render() {
    return <div>Hello {this.props.toWhat}</div>;
  }
}

ReactDOM.render(
  <Hello toWhat="World" />,
  document.getElementById('root')
);
```

可以被编译成不使用JSX 的代码：

```
class Hello extends React.Component {
  render() {
    return React.createElement('div', null, `Hello ${this.props.toWhat}`);
  }
}

ReactDOM.render(
  React.createElement(Hello, {toWhat: 'World'}, null),
  document.getElementById('root')
);
```

如果你想要看看JSX 如何改变成JavaScript 的更多例子，你可以在[this online Babel compiler](https://babeljs.io/repl/#?presets=react&code_lz=GYVwdgxgLglg9mABACwKYBt1wBQEpEDeAUIogE6pQhlIA8AJjAG4B8AEhlogO5xnr0AhLQD0jVgG4iAXyJA) 上尝试。

组件可以是一个字符串，也可以是一个`React.Component` 子类，也可以是一个纯函数的无状态组件.

如果你已经对写`React.createElement` 感到非常厌倦了，一个通用的方式是赋给一个简写：

```
const e = React.createElement;

ReactDOM.render(
  e('div', null, 'Hello World'),
  document.getElementById('root')
);
```

如果你使用`React.createElement` 的简写形式，它几乎和使用不带JSX 的React 一样方便。

或者，你可以参考社区项目，例如[react-hyperscript](https://github.com/mlmorg/react-hyperscript) 和[hyperscript-helpers](https://github.com/ohanhi/hyperscript-helpers)，它们提供更简洁的语法。
