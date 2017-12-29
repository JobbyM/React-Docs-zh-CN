> 此文章是翻译[Context](https://reactjs.org/docs/context.html)这篇React（版本v16.2.0）官方文档。

## Context

>**注意：**

>从React v15.5起，`React.PropTypes` 已经被移动到一个不同的包，我们建议使用[`prop-types`库](https://www.npmjs.com/package/prop-types)来定义`contextTypes`。

>我们提供了[a codemod script](https://reactjs.org/blog/2017/04/07/react-v15.5.0.html#migrating-from-react.proptypes) 来自动转换。

使用React ，很容易追踪通过你的React 组件的流动的数据。但你察看一个组件时，你可以看到那一个props 被传入，这使得你的应用很容易推理。

在一些情况下，你希望通过组件树传递数据，而不需要在每一级手动通过props 进行传递。你可以在React 中直接使用强大的“context” API 实现它。

### Why Not To Use Context

绝大多数应用不需要使用context。

如果你想要你的应用是健壮的，就不能使用context。这是一个实验性API并且它很可能在未来React 的发布版本中改变。

如果你不熟悉像[Redux](https://github.com/reactjs/redux) 和[MobX](https://github.com/mobxjs/mobx) 这种state 管理库，不要使用context。在许多实际应用中，这些库以及和它们的React 绑定是一个很好的管理和许多组件相关的state。更有可能的是Redux 和context 相比是一个很好的解决方案。

如果你不是一个经验丰富的React 开发者，不要使用context。这通常有只需要使用props 和state 就能实现这些功能。

如果你坚持使用context 而不管这些警告，尝试将你使用的context 独立在一小块区域中避免直接使用这些context API，以便当这些API 改变后便于升级。

### How To Use Context

假设你有一个如下的结构：

{% raw %}
```
class Button extends React.Component {
  render() {
    return (
      <button style={{background: this.props.color}}>
        {this.props.children}
      </button>
    );
  }
}

class Message extends React.Component {
  render() {
    return (
      <div>
        {this.props.text} <Button color={this.props.color}>Delete</Button>
      </div>
    );
  }
}

class MessageList extends React.Component {
  render() {
    const color = "purple";
    const children = this.props.messages.map((message) =>
      <Message text={message.text} color={color} />
    );
    return <div>{children}</div>;
  }
}
```
{% endraw %}

在这个例子中，我们以适当的方式手动得传入一个`color` props 到`Button` 和`Message` 组件中。使用context，我们可以通过这个树自动传入：

{% raw %}
```
import PropTypes from 'prop-types';

class Button extends React.Component {
  render() {
    return (
      <button style={{background: this.context.color}}>
        {this.props.children}
      </button>
    );
  }
}

Button.contextTypes = {
  color: PropTypes.string
}

class Message extends React.Component {
  render() {
    return (
      <div>
        {this.props.text} <Button>Delete</Button>
      </div>
    );
  }
}

class MessageList extends React.Component {
  getChildContext(){
    return {color: "purple"};
  }

  render() {
    const children = this.props.messages.map((message) =>
      <Message text={message.text} />
    );
    return <div>{children}</div>;
  }
}

MessageList.childContextTypes = {
  color: PropTypes.string
};
```
{% endraw %}

通过添加`childContextTypes` 和`getChildContext` 到`MessageList`（context 提供者），React 可以自动向下传递信息并且子树中的任何组件（这个例子中的`Button`）都可以通过定义`contextTypes` 去访问它。  

如果`contextTypes` 没有被定义，那么这个`context` 将是一个空对象。

### Parent-Child Coupling

Context 也允许你创建一个父子通信的API。例如，[React Router V4](https://reacttraining.com/react-router) 就是使用这种方式的一个库：

```
import { BrowserRouter as Router, Route, Link } from 'react-router-dom';

const BasicExample = () => (
  <Router>
    <div>
      <ul>
        <li><Link to="/">Home</Link></li>
        <li><Link to="/about">About</Link></li>
        <li><Link to="/topics">Topics</Link></li>
      </ul>

      <hr/>

      <Route exactly pattern="/" component={Home} />
      <Route pattern="/about" component={About} />
      <Route pattern="/topics" component={Topics} />
    </div>
  </Router>
);
```

从`Router` 组件向下传递一些信息，每一个`Link` 和`Route` 都可以回传到包含容器`Router`。

在你使用类似的API 构建组件之前，考虑是否有一个更好的替代方案。例如，如果你喜欢你可以将整个React 组件作为props 传入。

### Referencing Context in Lifecycle Methods

如果`contextTypes` 在component 中被定义，下面这些[lifecycle methods](https://reactjs.org/docs/react-component.html#the-component-lifecycle) 将会接受一个额外的context 对象作为参数：

* [constructor(props, context)](https://reactjs.org/docs/react-component.html#constructor)
* [componentWillReceiveProps(nextProps, nextContext)](https://reactjs.org/docs/react-component.html#componentwillreceiveprops)
* [shouldComponentUpdate(nextProps, nextState, nextContext)](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)
* [componentWillUpdate(nextProps, nextState, nextContext)](https://reactjs.org/docs/react-component.html#componentwillupdate)

>**注意：**

>自从React 16，`componentDidUpdate` 不再接受`prevContext`。

### Referencing Context in Stateless Functional Components

无状态的函数式组件也可以引用`context` 如果`contextTypes` 作为函数的属性被定义。下面代码展示一个无状态的函数式`Button` 组件。

{% raw %}
```
const PropTypes = require('prop-types');

const Button = ({children}, context) =>
  <button style={{background: context.color}}>
    {children}
  </button>;

Button.contextTypes = {color: PropTypes.string};
```
{% endraw %}

### Updating Context

不要做它。

React 有一个可以更新context 的API，但是它会从根本上造成破坏，所以你不应该使用它。

当state 和props 改变时，就会调用`getChildContext` 函数。为了更新context 中的数据，使用`this.setState` 触发本地state 更新。这将会产生新的context 并且改变会被子节点收到。

```
const PropTypes = require('prop-types');

class MediaQuery extends React.Component {
  constructor(props);
  this.state = {type: 'desktop'};

  getChildContext() {
    return {type: this.state.type};
  }

  componentDidMount() {
    const checkMediaQuery = () => {
      const type = window.matchMedia("(min-width: 1025px)").matches ? 'desktop' : 'mobile';
      if (type !== this.state.type) {
        this.setState({type});
      }
    };

    window.addEventListener('resize', checkMediaQuery)
    checkMediaQuery();
  }

  render() {
    return this.props.children;
  }
}

MediaQuery.childContextTypes = {
  type: PropTypes.string
};
```

问题是，如果组件提供的context 值发生改变，后代使用这个值将不会更新如果中间件的父的`shouldComponentUpdate` 返回false。使用context 这个组件是完全失去控制，所以这里基本没有办法依赖更新context。[这篇文章](https://medium.com/@mweststrate/how-to-safely-use-react-context-b7e343eff076) 解释了为什么这是一个问题并且你怎么避免它。
