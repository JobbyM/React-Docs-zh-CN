> 此文章是翻译[React Without JSX](https://facebook.github.io/react/docs/react-without-jsx.html)这篇React（版本v15.4.0）官方文档。

## React Without JSX

JSX 对使用React 不是必须的。当你不想在你的构建环境中设置编译，使用不带JSX 的React 是非常方便的。

每一个JSX element 只是调用`React.createElement(component, props, ...children)` 的语法糖（syntactic sugar）。所以，任何你使用JSX 可以做的事情都可以使用纯JavaScript 来实现。

例如，下面代码使用JSX ：
```jsx
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
可以被编译成不实用JSX 的代码：
```jsx
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
如果你想要看看JSX 如何改变成JavaScript 的更多例子，你可以在[thisonline Babel compiler](https://babeljs.io/repl/#?babili=false&evaluate=true&lineWrap=false&presets=es2015%2Creact%2Cstage-0) 上尝试。

component 可以是提供一个字符串，可以提供一个`React.Component` 子类，可以是一个纯函数为无状态component.

如果你已经对写`React.createElement` 感到非常厌倦了，一个通用的方式是赋给一个简写：
```jsx
const e = React.createElement;

ReactDOM.render(
  e('div', null, 'Hello World'),
  document.getElementById('root')
);
```
如果你使用`React.createElement` 的简写形式，它几乎和使用不带JSX 的React 一样方便。
