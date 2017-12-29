> 此文章是翻译[React Without ES6](https://reactjs.org/docs/react-without-es6.html)这篇React（版本v16.2.0）官方文档。

## React Without ES6

通常你可以使用一个纯JavaScript 类去定义一个React 组件：

```
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

如果你不使用ES6，你也可以使用`create-react-class` 模块来代替：

```
var createReactClass = require('create-react-class');
var Greeting = createReactClass({
  render: function() {
    return <h1>Hello, {this.props.name}</h1>;
  }
});
```

ES6 类API 的使用除了几个例外和`createReactClass()` 非常相似。

### Declaring Default Props

在函数和ES6 类中`defaultProps` 被定义为组件自身的属性：

```
class Greeting extends React.Component {
  // ...
}

Greeting.defaultProps = {
  name: 'Mary'
};
```

使用`createReactClass()`，你需要定义`getDefaultProps()` 作为一个传入对象方法：

```
var Greeting = createReactClass({
  getDefaultProps: function() {
    return {
      name: 'Mary'
    };
  },

  // ...

});
```

### Setting the Initial State

在ES6 类中，你可以在构造函数中通过给`this.state` 赋值来定义初始state：

```
class Greeting extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: props.initialCount};
  }
  // ...
}
```

使用`createReactClass()`，你必须提供一个单独的`getInitialState` 方法去返回初始state：

```
var Greeting = createReactClass({
  getInitialState: function() {
    return {count: this.props.initalCount};
  },
 // ...
});
```

### Autobinding

在React 组件 中使用ES6 类声明，方法和正规ES6 类语法一样。这意味着它们不会自动绑定`this` 到实例上。你必须在构造函数中明确地使用`.bind(this)`：

```
class SayHello extends React.Component {
  constructor(props) {
    super(props);
    this.state = {message: 'Hello!'};
    // This line is important!
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    alert(this.state.message);
  }

  render() {
    // Because `this.handleClick` is bound, we can use it as an event handler
    return (
      <button onClick={this.handleClick}>
        say hello
      </button>
    );
  }
}
```

使用`createReactClass()` ，这就不是必须的，因为它绑定了所有的方法：

```
var SayHello = createReactClass({
  getInitialState: function() {
    return {message: 'Hello!'};
  },

  handleClick: function() {
    alert(this.state.message);
  },

  render: function() {
    return (
      <button onClick={this.handleClick}>
        say hello
      </button>
    );
  }
});
```

这意味着写ES6 类会遇到更多一点的样板代码对事件句柄，好处是是在大型应用中有更好的性能。

如果模版代码不吸引你，你可以使用Babel 的 **实验性**[Class 属性](https://babeljs.io/docs/plugins/transform-class-properties/)语法提议：

```
class SayHello extends React.Component {
  constructor(props){
    super(props);
    this.state = {message: 'Hello!'};
  }
  // WARNING: this syntax is experimental!
  // Using an arrow heer binds the method:
  handleClick = () => {
    alert(this.state.message);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        say hello
      </button>
    );
  }
}
```

请注意上述的一份是 **实验性的** 并且语法可能会变化，或者这个提议不会进入语言。

如果你更安全的使用它，你有一下几个选择：

* 在构造函数中绑定方法。
* 使用箭头函数，例如：`onClick={(e) => this.handleClick(e)}`。
* 保持使用`createReactClass()`。

### Mixins

>**注意：**

>ES6 本身不支持任何混合（mixin）。因此，当你在React 使用ES6 类时，也不支持混合。

>**我们在使用混合的代码库中发现大量问题**[不建议在新代码中使用它们](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)

>这部分只是为了参考而存在。

有时不同的组件会共享相同的功能。这称为[横切关注点（cross-cutting concerns）](https://en.wikipedia.org/wiki/Cross-cutting_concern)。`createReactClass` 让你使用遗留的`mixins` 为此。

一个常见的用例是在时间间隔上更新组件自身。使用`setInterval()` 是简单的，但是当你不再使用它去保存记忆时，去取消它是重要的。React 提供[声生命周期方法](https://reactjs.org/docs/state-and-lifecycle.html)去让你知道什么时候去创建或销毁一个组件。让我们创建一个简单的mixin 使用这些方法去提供一个简单的`setInterval()` 方法去自动清理，当你的组件被销毁时：

```
var SetIntervalMixin = {
  componentDidMount: function() {
    this.intervals = [];
  },
  setInterval: function() {
    this.intervals.push(setInterval.apply(null, arguments));
  },
  componentWillUnmount: function() {
    this.intervals.forEach(clearInterval);
  }
}

var createReactClass = require('create-react-clcass');

var TickTock = createReactClass({
  mixins: [SetIntervalMixin], // use the mixin
  getInitialState: function() {
    return {seconds: 0}
  },
  componentDidMount: function() {
    this.setInterval(this.tick, 1000) // Call a method on the mixin
  },
  tick: function() {
    this.setState({seconds: this.state.seconds + 1});
  },
  render: function() {
    return (
      <p>
        React has been running for {this.state.seconds} seconds.
      </p>
    );
  }
});

ReactDOM.render(
  <TickTock />,
  document.getElementById('example')
);
```

如果一个组件使用复杂的mixins 或几个mixins 定义同一个生命周期方法（例如：几个mixins 方法想要在组件销毁时进行清理工作），所有的生命周期方法都会保证被调用。方法被定义mixins 上去按照mixins 列表中的顺序运行，随着组件的方法调用。
