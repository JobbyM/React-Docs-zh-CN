> 此文章是翻译[handling-events](https://reactjs.org/docs/handling-events.html)这篇React（版本v16.2.0）官方文档。

## Handling Event

React 元素处理事件和DOM 元素事件处理非常类似。只有几个语法不同：

* React 事件以camelCase 方式命名，而不是lowercase。
* 在JSX 中以函数作为事件句柄（event handler），而不是字符串

例子，这HTML：

```
<button onclick="activateLasers()">
  Activate Lasers
</button>
```

同React 有轻微不同：

```
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

另一个不同就是在React 中你不能通过返回`false` 来阻止默认事件发生。你必须明确的调用`preventDefault`。例如，在纯HTML 中，阻止一个链接跳转打开一个新页面，你可以这样写：

```
<a href="#" onclick="console.log('The link is clicked.'); return false">Click me</a>
```

在React 中，可以通过以下方式来代替：

```
function ActionLink(){
  function handleClick(e){
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```

这里`e` 是一个合成事件（synthetic event）。React 根据[W3C spce](https://www.w3.org/TR/DOM-Level-3-Events/)来定义合成事件（synthetic event），所以你不必担心跨浏览器适配（cross-browser compatibility）问题。参考[`SyntheticEvent`](https://reactjs.org/docs/events.html) 了解更多。

在使用React 时通常你不需要在一个DOM 元素创建之后，调用`addEventListener`去添加一个侦听器。相反，只需要在元被初次渲染时添加一个侦听器就可以了。

当你使用[ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 定义一个组件时，通常的做法是在类中添加一个方法作为事件句柄。例如，这个`Toggle` 组件就是渲染一个按钮（button）让用户在“ON”和“OFF”状态之间切换：

```
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this)
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```

[在CodePen 上尝试](http://codepen.io/gaearon/pen/xEmzGg?editors=0010)

你必须注意在JSX 回调函数中`this` 的含义。在JavaScript 中，类方法并不是默认绑定的（[bound](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind)）。如果你忘记绑定`this.handleClick` 并把它传递给`onClick`，在实际被调用时，`this` 的值将会是`undefined`。

这并不是React-specific（特性）行为；它只是[how functions work in JavaScript](https://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/)。通常如你引用一个函数并没有带`()`在其函数后面，例如`onClick={this.handleClick}` ，你需要绑定这方法。

如果调用`bind` 令你烦恼，这里有两种方法让你绕开它。如果你正在使用试验性的[公共类字段语法](https://babeljs.io/docs/plugins/transform-class-properties/)，你可以使用类字段去正确的绑定回调函数：

```
class LoggingButton extends React.Component {
  // This syntax ensures `this` is bound within handleClick
  // Waring: this is *experimental* syntax.
  handleClick = () => {
    console.log('This is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```
这个语法在[Create React App](https://github.com/facebookincubator/create-react-app) 是默认可用的。

如果你不使用类字段语法，你可以在回调函数中使用箭头函数（[arrow function](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)）：

```
class LoggingButton extends React.Component {
  handleClick() {
    console.log('This is:', this);
  }

  render() {
    // This syntax ensures 'this' is bound within handleClick
    return(
      <button onClick={(e)=>this.handleClick(e)}>
        Click me
      </button>
    );
  }
}
```

这个语法的问问题是当`LoggingButton` 每一次被渲染时，都会创建一个不同的回调函数。在大多数情况下，这是可以承受的。然而，如果这个函数被作为prop 传递给子组件（lower components），这些组件可能还会做一次额外的渲染（an extra re-rendering）。我们通常建议在构造函数中绑定或使用类字段语法，来避免这类性能问题。

### Passing Arguments to Event Handlers

在一个循环中，通常想要传递额外的参数到事件处理器中。例如，如果`id` 是行ID，下面两种方法都可以工作：

```
<button onClick={(e) => this.deleleRow(id, e)}>Delete Row</button>
<button onClick={this.deleleRow.bind(this, id)}>Delete Row</button>
```

尚明两行是相等的，分别使用[arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) 和[Function.prototype.bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind)。

在这两个例子中，`e` 参数作为React 事件将会作为ID 之后的第二个参数被传入。在箭头函数中，我们必须显示地传入，但是在`bind` 中，任何更多的参数被自动地提前。
