> 此文章是翻译[composition vs inheritance](https://facebook.github.io/react/docs/composition-vs-inheritance.html)这篇React（版本v15.4.0）官方文档。

## Composition vs Inheritance

React 有一个非常强大的组合模式（composition model），在组件之间，我们建议使用组合（composition）而不是继承（inheritance）。

在这个部分，我们将会考虑几个对React 开发新手来说经常遇到的几个继承问题，展示我们如何使用组合方式来解决它们。

### Containment

一些组件提前并不知道它们的孩子元素（children）。对`Sidebar` 或者`Dialog` 这些作为"boxes" 的组件来说。

我们通常建议这种组件使用`children` prop 作为孩子元素（children elments）直接作为输出：
```jsx
function FancyBorder(props){
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  )
}
```
在JSX 嵌套中可以让其他组件直接传入任意孩子：
```jsx
function WelcomeDialog(){
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
在`<FancyBorder>` 标签中的任何东西都可以作为`FancyBorder` 组件的`children` prop 传入。由于`Fancyborder` 在`<div>` 标签中渲染`{props.chidren}` ，所以传入的elements 最终被输出显示。

虽然这不是常见的的，但是有时候，你需要多个"holdes"，在一个component 中。在这种情况下，你可以根据你自己的习惯来替换使用`children`：
```jsx
function SplitPanel(props){
  return (
    <div className="SplitPanel">
      <div className="SplitPanel-left">
        {props.left}
      </div>
      <div className="SplitPanel-right">
        {props.right}
      </div>
    </div>
  )
}

function App(){
  return (
    <SplitPanel
      left={
        <Contacts />
      }
      right={
        <Chat />
      }/>
  )
}
```
`<Contacts />` 和`<Chat />` 这种React elements 就是一个object，你可以像其它数据一样将其作为props 传入。

### Specialization

有时我们考虑到组件可以作为其它组件的“special cases”。例如，我们可以说`WelcomeDialog` 是`Dialog` 的一个“special case ”。

在React 中，这样可以通过组合来实现，通过使用props 来配置实现在一个 “special” 组件中在一个“generic”中渲染：
```jsx
function Dialog(props){
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
    </FancyBorder>
  )
}
function WelcomeDialog(){
  return (
    <Dialog
      title="Welcome"
      message="Thank you for visting our spacecraft!"/>
  )
}
```
通过类定义的组件也可以使用组合方式：
```jsx
function Dialog(props){
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
  )
}
class SignUpDialog extends Component {
  constructor(props){
    super(props)
    this.handleChange = this.handleChange.bind(this)
    this.handleSignUp = this.handleSignUp.bind(this)
    this.state = {
      login: ''
    }
  }

  handleChange(event){
    this.setState({
      login: event.target.value
    })
  }

  handleSignUp(){
    alert(`Welcome aboard, ${this.state.login}!`)
  }

  render(){
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
}
```
### So What About Inheritance?

在Facebook 中，我们使用成千上万个组件，我们还没有发现任何需要继承来实现的组件。

Props 和组合可以让你灵活的配置组件的样式和行为通过准确和安全的方式。记住组件可以接受任何props，包括原生值（primitive values），React elements 或者是functions。

如果你想在组件之间复用无显示（non-UI）功能，我们建议提取一个独立的JavaScript module。这个组件无需扩展就可以通过import 来使用这个function，object，或者一个class。
