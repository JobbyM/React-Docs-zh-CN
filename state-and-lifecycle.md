> 此文章是翻译[State and Lifecycle](https://reactjs.org/docs/state-and-lifecycle.html)这篇React（版本v16.2.0）官方文档。

## State and Lifecycle

考虑[之前章节](https://reactjs.org/docs/rendering-elements.html#updating-the-rendered-element)中的时钟例子。

到现在为止，我们只会通过一种方式进行更新UI。

我们通过调用`ReactDOM.render()` 方法去改变渲染输出：

```
function tick() {
  const element = (
    <div>
      <h1>Hello World!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('root')
  )
}

setInterval(tick, 1000)
```

[在CodePen 上尝试](http://codepen.io/gaearon/pen/gwoJZk?editors=0010)

在这一章节中，我们将学习如何使`Clock` 组件真正可复用和封装。它将设置自己的定时器来每一秒更新它自己。

我们开始封装始时钟外观：

```
function Clock(props) {
  return (
    <div>
      <h1>Hello World!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick(){
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  )
}

setInterval(tick, 1000)
```

[在CodePen 上尝试](http://codepen.io/gaearon/pen/dpdoYR?editors=0010)

但是，它忽略了一个关键的需求：一个事实，即`Clock` 设置一个定时器来每隔一秒进行更新UI ，应该是`Clock` 的实现细节。

理想状况下，我们只写一次来让`Clock` 更新自身：

```
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  )
```

为了实现，我们需要在`Clock` 组件中添加“state”。

State 同props 非常相似，但是它是私有的完全被组件控制。

我们[之前提到过](https://reactjs.org/docs/components-and-props.html#functional-and-class-components) 组件使用类定义有额外的特性（additional features）。本地State 就是这样：只能通过类提供此功能。

### Converting a Function to a Class

你可以通过五步来实现将一个函数式组件类似`Clock` 转成类：

1. 使用相同的名字实现一个[ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 并继承`React.Component`。
2. 添加一个`render()` 空方法。
3. 将函数体内容移到`render()` 方法内。
4. 在`render()` 方法体内将`props` 替换为`this.props`。
5. 删除空的函数声明。

```
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello World!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

[在CodePen 上尝试](http://codepen.io/gaearon/pen/zKRGpo?editors=0010)

`Clock` 现在是通过类定义而不是组件。

现在我们可以使用额外的特性例如本地state 和生命周期钩子。

### Adding Local State to a Class

通过三步将`date` 从props 移到state：
1）在`render()` 方法中 将`this.props.date` 替换为 `this.state.date`：

```
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello World!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

2）添加一个[class constructor](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes#Constructor) 并给`this.state` 赋初值：

```
class Clock extends React.Component {
  constructor(props){
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello World!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

注意，我们将`props` 传入了基本的构造函数中：

```
  constructor(props){
    super(props);
    this.state = {date: new Date()};
  }
```

类组件应该总是使用`props` 调用基本的构造函数。

3）将`date` 从`<Clock />` 元素中移除：

```
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

稍后我们将组件自身添加定时器。

结果看上去如下：

```
class Clock extends React.Component {
  constructor(props){
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello World!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

[在CodePen 上尝试](http://codepen.io/gaearon/pen/KgQpJd?editors=0010)

接下来，我们将给`Clock` 设置定时器并每秒更新一次。

### Adding Lifecycle Methods to a Class

在一个使用许多组件的应用中，当组件被销毁时去释放资源是非常重要的。

无论什么时候当`Clock` 第一次渲染到DOM 时，我们要[设置一个定时器](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setInterval)。在React 中被称为“挂载（mounting）”。

无论什么时候当`Clock` 产生的DOM 被移除时去，我们也要[清除定时器](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/clearInterval)。在React 中被称为“卸载（unmounting）”。

当一个组件被挂载和卸载时，我们将在通过在类组件中声明特殊的方法（special methods）去运行一些代码：

```
class Clock extends React.Component {
  constructor(props){
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {

  }

  componentWillUnmount() {

  }

  render() {
    return (
      <div>
        <h1>Hello World!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

这些方法被称为“（生命周期钩子）lifecyle hooks”。
这个`componentDidMount()` 钩子是在组件的输出已经被渲染到DOM 中之后运行。这是一个好地方去设置一个定时器：

```
  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }
```

注意我们将定时器ID 保存在`this` 上。

虽然`this.props` 是被React 自身设置的的但是`this.state` 有特殊的含义，如果你要存储一些不用于可视化输出的东西，你可以手动自由添加额外的字段到类上。

如果你不需要在`render()` 中使用一些东西，它不应该存储在state 中。

我们会在`componentWillUnmount()` 生命周期钩子中销毁这个定时器：

```
  componentWillUnmount() {
    clearInterval(this.timerID);
  }
```

最后，我们将实现这个`tick()` 方法，`Clock` 组件每秒运行一次。

它将会使用`this.setState()` 去调度更新组件的本地 state：

```
class Clock extends React.Component {
  constructor(props ){
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello World!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

[在CodePen 上尝试](http://codepen.io/gaearon/pen/amqdNA?editors=0010)

现在这个时钟每秒滴答一次。

让我们快速回顾一下每一步都调用那个方法：

1）当将`<Clock />` 传给`ReactDOM.render()` 方法时，React 调用 `Clock` 组件的构造器方法。由于`Clock` 需要展示当前时间，它使用一个包含当前时间的对象初始化`this.state`。我们之后将更新这个state。

2）React 然后调用`Clock` 组件的`render()` 方法。这是React 了解要在屏幕上展示什么。React 然后更新DOM 去匹配`Clock` 的渲染输出。

3）当`Clock` 输出被插入到DOM 中，React 调用`componentDidMount()` 生命周期钩子。在它内部，`Clock` 组件要求浏览器设置一个定时器去每隔一秒调用`tick()`。

4）每隔一秒浏览器就调用`tick()`方法。在它内部，`Clock` 组件调度一个UI 更新通过调用一个包含当前时间对象的`setState()` 方法。由于`setState()` 调用，React 知道当前state 已经发生变化，于是又去调用`render()` 方法去了解屏幕上应该显示什么。这次，`this.state.date`在`render()` 中是不同的，所以渲染输出会包含当前时间。React 相应地更新DOM。

5）如果`Clock` 组件被从DOM 中移除，React 将会调用`componentWillUnmount()` 生命周期钩子所以这个定时器会停止。

### Using State Correctly

你需要知道关于`setState()` 的三件事。

#### Do Not Modify State Directly

例如，它将不会重新渲染组件：

```
// Wrong
this.state.comment = 'Hello';
```

相反，应该使用`setState()`：

```
// Correct
this.setState({comment: 'Hello'});
```

唯一能够直接赋值`this.state`的是在构造器中。

#### State Updates May Be Asynchronous

为了性能React 可能批处理多个`setState()` 调用在一次更新中。

因为`this.props` 和`this.state` 可以异步更新，你不应该依赖它们中任意值去计算下一个state。

例如，下面代码更新计数器会可能会失败：

```
  // Wrong
  this.setState({
    counter: this.state.counter + this.props.increment
  })
```

为了解决这个问题，使用另一种方式的`setState()` 接受一个函数而不是一个对象。这个函数可以接受之前的state 作为第一个参数，此时props 可以被应用为第二个参数：

```
  // Correct
  this.setState((prevState, props) => ({
    counter: prevState.counter + props.increment
  }));
```

在上面我们使用了[arrow function](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，但是我们使用常规函数也是可以工作的：
```jsx
  // Correct
  this.setState(function(prevState, props) {
    return {
      counter: prevState.counter + props.increment
    };
  });
```

#### State Updates are Merged

当你调用`setState()`，React 会合并你提供的对象到当前state 中。

例如，你的state 可能包含几个独立的变量：

```
constructor(props) {
  super(props);
  this.state = {
    posts: [],
    comments: []
  };
}
```

然后你可以独立地调用几个`setState()`方法：

```
componentDidMount() {
  fetchPosts().then(response => {
    this.setState({
      posts: response.posts
    });
  });

  fetchComments().then(response => {
    this.setState({
      comments: response.comments
    });
  });
}
```

合并操作是隐式的，所以`this.setState({comments})` 保留了`this.state.post` 完整，但是完全替换了`this.state.comments`。

### The Data Flows Down

父组件或子组件都不知道某一个组件是有状态的（stateful ） 还是无状态的（stateless ），并且它们也不管它是被定义为一个函数还是一个类。

这就是为什么state 经常被称为本地的（local）或者是封装的（encapsulated）。它不能被除拥有它之外的组件进行设置。

一个组件可以将它的state 作为子组件的props 进行传递：

```
<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
```

在用户自定义组件中工作：

```
<FormattedDate date={this.state.date} />
```

这个`FormattedDate` 组件在它的props 中接受`date` 而不需要知道它是来自`Clock` 的state ，还是props，亦或是手动输入的：

```
function FormattedDate(props) {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>
}
```

[在CodePen 上尝试](http://codepen.io/gaearon/pen/zKRqNB?editors=0010)

这就是自上而下（top-down）或单向的（unidirectional）数据流。任何state 都是有某些特定component 拥有的，任何来自该state 的数据或UI 都只能影响位于层级树之下的组件。

如果你设想一个组件树是一个props 的瀑布流，每一个组件的state 都像是一个在任意位置加入的额外的只能向下流的水源。

为了展示所有的组件都是独立的，我们可以创建一个`App` 组件去渲染三个`Clock`：

```
function App() {
  return (
    <div>
      <Clock />
      <Clock />
      <Clock />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

[在CodePen 上尝试](http://codepen.io/gaearon/pen/vXdGmd?editors=0010)

每一个`Clock` 都独立地设置它们自己的定时器进行更新。

在React 应用中，组件是否有状态是一个可以随着时间变化的实现细节。你可以在有状态的组件中使用无状态的组件，反之亦然。
