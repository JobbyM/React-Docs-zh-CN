> 此文章是翻译[conditional rendering](https://reactjs.org/docs/conditional-rendering.html)这篇React（版本v16.2.0）官方文档。

## Conditional Rendering

在React 中，你可以创建不同的组件，去封装你需要的行为。话说回来，你可以根据你的应用状态，只渲染其中部分组件。

在React 中条件渲染的工作流程同JavaScript 中的条件工作流程一样。利用JavaScript 操作符例如`if` 或者是条件操作符（[conditional operator](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)）去新建元素代表当前状态，然后让React 更新UI 去匹配它们。

考虑一下两个组件：

```
function UserGreeting(props) {
  return <h1>Welcome back!</h1>;
}

function GuestGreeting(props) {
  return <h1>Please sign up.</h1>;
}
```

我们新建一个`Greeting` 组件来基于一个用户是否已经登录这个两个状态来展示:

```
function Greating(props) {
  const isLoggedIn = props.isLoggedIn
  if(isLoggedIn){
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}

ReactDOM.render(
  // Try changeing to isLoggedIn={true}
  <Greeting isLoggedIn={false} />,
  document.getElementById('root')
);
```

[在CodePen 上尝试](https://codepen.io/gaearon/pen/ZpVxNq?editors=0011)

上面这个例子展示了根据`isLoggedIn` props 的值来渲染不同的greeting。

### Element Variables

你可以使用变量来存储元素。这样可以帮助你条件渲染一部分组件，同时其他剩余组件不改变。

考虑到下面两个新组件来代表Logout 和Login 按钮：

```
function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      Login
    </button>
  )
}

function LogoutButton(props) {
  return (
    <button onClick={props.onClick}>
      Logout
    </button>
  )
}
```

在下面这个例子中，我们将会创建一个叫`LoginControl` [有状态组件](https://reactjs.org/docs/state-and-lifecycle.html#adding-local-state-to-a-class)）。

它将根据当前状态要么渲染`<LoginButton />`要么渲染`<LogoutButton />`。它也会渲染之前例子中的`<Greeting />` 这个组件:

```
class LoginControl extends React.Component {
  constructor(props) {
    super(props);
    this.handleLoginClick = this.handleLoginClick.bind(this);
    this.handleLogoutClick = this.handleLogoutClick.bind(this);
    this.state = {isLoggedIn: false};
  }

  handleLoginClick() {
    this.setState({isLoggedIn: true});
  }

  handleLogoutClick() {
    this.setState({isLoggedIn: false});
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn;

    let button = null
    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick}/>;
    } else {
      button = <LoginButton  onClick={this.handleLoginClick} />;
    }

    return (
      <div>
        <Greating isLoggedIn={isLoggedIn}/>
        {button}
      </div>
    );
  }
}

ReactDOM.render(
  <LoginControl />,
  document.getElementById('root')
);
```

[在CodePen 上尝试](https://codepen.io/gaearon/pen/QKzAgB?editors=0010)

当声明了一个变量并且使用`if` 语句是一个好方式去条件渲染一个组件，有时你可能想要使用更短的语法（a shorter syntax）。下面来介绍几种在JSX 中使用行内条件（inline conditions）

### Inline if with Logical && Operator

你可以[在JSX 中嵌入任何表达式](https://reactjs.org/docs/introducing-jsx.html#embedding-expressions-in-jsx)）通过使用大括号包裹它们。这里包括JavaScript `&&`（逻辑与 ） 操作符。它可以很方便作为条件在一个元素中：

```
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unreadMessages.
        </h2>
      }
    </div>
  );
}

const messages = ['React', 'Re: React', 'Re:Re: React'];

ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('root')
);
```

[在CodePen 上尝试](https://codepen.io/gaearon/pen/ozJddz?editors=0010)

在JavaScript 中它是工作的，`true&&expression` 总是计算成`expression`，而`false&&expression` 总是计算成`false`。

因此，如果条件是`true`，位于`&&` 右侧的元素会在输出中显示，如果是`false`，React 会忽略并且跳过它。

### Inline If-Else with Condtional Operator

另一个行内条件渲染元素的方式是使用条件操作符[condition ? true : false](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)。

在下面这个例子中，我们使用它来条件渲染一小块文字。

```
  render() {
    const isLoggedIn = this.state.isLoggedIn;

    return (
      <div>
        The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
      </div>
    );
  }
```

它可以被用作大的表达式，虽然对将要发生什么并不明显：

```
  render() {
    const isLoggedIn = this.state.isLoggedIn;
    return (
      <div>
        {isLoggedIn ? (
          <LogoutButton onClick={this.handleLogoutClick} />
        ) : (
          <LoginButton onClick={this.handleLoginClick} />
        )}
      </div>
    );
  }
```

就像在JavaScript，它只依赖你和你的团队对考虑可读性来选择一个合适的方式。同时记住无论什么时候只要条件变得太复杂，去（提取出一个组件](https://reactjs.org/docs/components-and-props.html#extracting-components)）是更好的方式。

### Preventing Component from Rendering

在少数情况下，你可能要想隐藏一个组件，即使这个组件已经被另一个组件渲染了。通过返回`null` 来代替输出。

在下面这个例子中，这个`<WarningBanner />` 被渲染依赖`warn` prop 值。如果这个prop 的值是`false`，那么这个组件不被渲染：

```
function WarningBanner(props) {
  if(!props.warn){
    return null;
  }

  return (
    <div className="warning">
      Warninig!
    </div>
  );
}

class Page extends React.Component {
  constructor(props) {
    super(props);
    this.state = {showWarning: true};
    this.handleToggleClick = this.handleToggleClick.bind(this);
  }

  handleToggleClick() {
    this.setState((prevState) => ({
      showWarning: !prevState.showWarning
    }));
  }

  render() {
    return(
      <div>
        <WarningBanner warn={this.state.showWarning} />
        <button onClick={this.handleToggleClick}>
          {this.state.showWarning ? 'Hide': 'Show'}
        </button>
      </div>
    );
  }
}

ReactDOM.render(
  <Page />,
  document.getElementById('root')
);
```

[在CodePen 上尝试](https://codepen.io/gaearon/pen/Xjoqwm?editors=0010)

从组件的`render`方法返回`null`不会影响组件生命周期方法的触发。 例如，`componentWillUpdate` 和`componentDidUpdate` 仍将被调用。
