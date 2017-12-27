> 此文章是翻译[Composition vs Inheritance](https://reactjs.org/docs/composition-vs-inheritance.html)这篇React（版本v16.2.0）官方文档。

## Composition vs Inheritance

React 有一个非常强大的组合模式（composition model），在组件之间，我们建议使用组合（composition）而不是继承（inheritance）。

在这个部分，我们将会考虑几个对React 开发新手来说经常遇到的几个继承问题，展示我们如何使用组合方式来解决它们。

### Containment

一些组件提前并不知道它们的孩子元素（children）。对`Sidebar` 或者`Dialog` 这些通用的"boxes" 的组件来说尤其常见。

我们通常建议这种组件使用`children` prop 作为孩子元素（children elments）直接作为输出：

```
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}
```

在JSX 嵌套中可以让其他组件直接传入任意孩子：

```
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  )
}
```

[在CodePen 上尝试](http://codepen.io/gaearon/pen/ozqNOV?editors=0010)

在`<FancyBorder>` JSX 标签中的任何东西都可以作为`FancyBorder` 组件的`children` prop 传入。由于`Fancyborder` 在`<div>` 标签中渲染`{props.chidren}` ，所以传入的元素最终被输出显示。

虽然这不是常见的的，但是有时候，在一个组件中，你需要多个"holes"。在这种情况下，你可以根据你自己的习惯来替换使用`children`：

```
function SplitPanel(props) {
  return (
    <div className="SplitPanel">
      <div className="SplitPanel-left">
        {props.left}
      </div>
      <div className="SplitPanel-right">
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPanel
      left={
        <Contacts />
      }
      right={
        <Chat />
      }/>
  );
}
```

[在CodePen 上尝试](http://codepen.io/gaearon/pen/gwZOJp?editors=0010)

`<Contacts />` 和`<Chat />` 这种React 元素就是一个object，你可以像其它数据一样将其作为props 传入。此方法可能会使你记起在其他库中的“插槽（slots）”，但对你在React 作为props 传入的内容没有任何限制。

### Specialization

有时我们考虑到组件可以作为其它组件的“特例（special cases）”。例如，我们可以说`WelcomeDialog` 是`Dialog` 的一个“特例”。

在React 中，这也可以通过组合来实现，通过使用props 来配置实现在一个 “特殊的（special）” 组件中中渲染一个“常用的（generic）”组件：

```
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
    </FancyBorder>
  );
}

function WelcomeDialog(){
  return (
    <Dialog
      title="Welcome"
      message="Thank you for visting our spacecraft!"/>
  );
}
```

[在CodePen 上尝试](http://codepen.io/gaearon/pen/kkEaOZ?editors=0010)

通过类定义的组件也可以使用组合方式：

```
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
      {props.children}
    </FancyBorder>
  );
}

class SignUpDialog extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.handleSignUp = this.handleSignUp.bind(this);
    this.state = {login: ''};
  }

  render() {
    return (
      <Dialog
        title="Mars Exploration Program"
        message="How should we refer to you ?">
        <input
          value={this.state.login}
          onChange={this.handleChange} />
        <button
          onClick={this.handleSignUp}>
          Sign Me Up!
        </button>
      </Dialog>
    )
  }

  handleChange(e) {
    this.setState({login: e.target.value});
  }

  handleSignUp() {
    alert(`Welcome aboard, ${this.state.login}!`);
  }
}
```

[在CodePen 上尝试](http://codepen.io/gaearon/pen/gwZbYa?editors=0010)

### So What About Inheritance?

在Facebook 中，我们使用了React 成千上万个组件，我们还没有发现任何需要继承来实现的组件。

Props 和组合可以让你灵活的配置组件的样式和行为通过准确和安全的方式。记住组件可以接受任何props，包括原生值（primitive values），React 元素或者是函数。

如果你想在组件之间复用非UI（non-UI）功能，我们建议提取一个独立的JavaScript 模块。这个组件无需扩展就可以通过import 来使用这个函数，对象，或者一个类。
