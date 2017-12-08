> 此文章是翻译[Passing Functions to Components](https://reactjs.org/docs/faq-build.html)这篇React（版本v16.2.0）官方文档。

## Passing Functions to Components

### 我如何传递一个事件处理函数（比如onClick）给组件？

将事件处理函数和其他函数作为props 传递给子组件：

```
<button onClick={this.handleClick}>
```

如果你需要在这个处理器中访问父组件，你也需要绑定（bind）这个函数到组件实例上（看下面）。

### 我如何绑定函数到组件实例上？

这有几种方式可以确保函数能够方法组件特性（attribute）像`this.props` 和`this.state`，取决于你使用的语法和构建步骤。

#### 在构造函数中绑定 (ES2015)

```
class Foo extends Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this)
  }
  handleClick() {
    console.log('Click happened');
  }
  render() {
    return <button onClick={this.handleClick}>Click Me</button>
  }
}
```

#### 类属性（第三阶段提案）

```
class Foo extends Component {
  // Note: this syntax is experimental and not standardized yet.
  handleClick = () => {
    console.log('Click happened');
  }
  render() {
    return <button onClick={this.handleClick}>Click Me</button>
  }
}
```

#### 在Render 中绑定

```
class Foo extends Component {
  handleClick() {
    console.log('Click happened');
  }
  render() {
    return <button onClick={this.handleClick.bind(this)}>Click Me</button>
  }
}
```

> 注意：
在render 方法中使用`Function.prototype.bind` 会在每次组件渲染时创建一个新的函数，这可能会有性能问题（看下面）。

#### 在Render 中使用箭头函数

```
class Foo extends Component {
  handleClick() {
    console.log('Click happened');
  }
  render() {
    return <button onClick={() => this.handleClick()}>Click Me</button>
  }
}
```

> 注意：
在render 方法中使用箭头函数会在每次组件渲染时创建一个新的函数，这可能会有性能问题（看下面）。


### 在render 方法中使用箭头函数是是否可以？

一般来说是可以的，并且这是最简单的向回调函数中传递参数的方法。

如果你有性能问题，一定要优化！

### 为什么绑定是必要的？

在JavaScript 中，下面代码片段是**不**相等的：

```
obj.method();
```

```
var method = obj.method;
method();
```

绑定方法确保第二个片段同第一个片段一样运行。

在React 中，通常你只需要绑定传递给其他组件的方法。例如，`<button onClick={this.handleClick}>` 传递`this.handleClick`，所以你需要绑定他。但是，没有必要版定`render` 方法或生命周期方法：我们不传递他们给其他组件。

[This post by Yehuda Katz](http://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/) 详细解释了在JavaScript 中，什么是绑定，以及函数如何工作。

### 为什么组件每次渲染时函数都会被调用？

确保每次将函数传递给组件时没有调用它：

```
render() {
  // Wrong: handleClick is called instead of passed as a reference!
  return <button onClick={this.handleClick()}>Click Me</button>
}
```

而是传递函数本身（不加括号）：
```
render() {
  // Correct: handleClick is passed as a reference!
  return <button onClick={this.handleClick}>Click Me</button>
}
```

### 怎样为事件处理程序或回调函数传递参数？

你可以使用箭头函数去包裹一个事件处理器来传递参数：

```
<button onClick={() => this.handleClick(id)} />
```

这和调用`.bind` 是等同的：

```
<button onClick={this.handleClick.bind(this, id)} />
```

#### 例子：使用箭头函数传递参数

```
const A = 65 // ASCII character

class Alphabet extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
    this.state = {
      justClicked = null,
      letters: Array.from({length: 26}, (_, i) => String.fromCharCode(A + i))
    };
  }
  handleClick(letter) {
    this.setState({ justClicked: letter})
  }
  render() {
    return (
      <div>
        Just clicked: {this.state.justClicked}
        <ul>
          {this.state.letters.map(letter =>
            <li key={letter} onClick={() => this.handleClick(letter)}>
              {letter}
            </li>
          )}
        </ul>
      </div>
    )
  }
}
```

#### 例子：使用data-attributes 传递参数

同样的，也可以使用DOM API来存储事件处理程序需要的数据。如果需要优化大量元素或使用依赖于React.PureComponent的渲染树，请考虑使用此方法。

```
const A = 65 // ASCII character

class Alphabet extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
    this.state = {
      justClicked = null,
      letters: Array.from({length: 26}, (_, i) => String.fromCharCode(A + i))
    };
  }

  handleClick(e) {
    this.setState({
      justClicked: e.target.dataset.letter
    });
  }

  render() {
    return (
      <div>
        Just clicked: {this.state.justClicked}
        <ul>
          {this.state.letters.map(letter =>
            <li key={letter} data-letter={letter} onClick={this.handleClick}>
              {letter}
            </li>
          )}
        </ul>
      </div>
    )
  }
}
```

### 怎样避免函数被调用太快或者太多次？

如果你有一个事件处理程序，就像`onClick` 或者`onScroll`，并且想要函数避免被快速触发，那么你可以限制回调函数执行的速度。可以使用下面几种方式实现：

* ***throttling***: 基于时间的频率来进行更改（例如[`_.throttle`](https://lodash.com/docs#throttle)）
* ***debouncing***: 在一段时间的不活动之后提交更改（例如[`_.debouncing`](https://lodash.com/docs#debounce)）
* ***requestAnimationFrame throttling***: 基于[requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) 进行更改（例如[`raf-schd`](https://github.com/alexreardon/raf-schd)）

看[this visulization](http://demo.nimius.net/debounce_throttle/) 这个对`throttle` 和`debounce` 函数的比较

> 注意：
> `_.debounce` , `_.throttle`和`raf-schd` 都提供了一个 cancel方法来取消延迟回调。 你要么在componentWillUnmount 这个生命周期方法中调用这个方法，要么对代码进行检查来保证在延迟函数有效期间内组件始终挂载。

#### Throttle（节流）

节流是阻止函数在给定窗口时间内被多次调用。下面这个例子会阻止“click”事件在一秒内最多调用一次。

```
import throttle from 'lodash.throttle';

class LoadMoreButton extends React.Component {
  construtor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
    this.handleClickThrottled = throttle(this.handleClick, 1000);
  }

  componentWillUnmount() {
    this.handleClickThrottled.cancel();
  }

  render() {
    return <button onClick={this.handleClickThrottled}>Load More</button>;
  }

  handlClick() {
    this.props.loadMore();
  }
}
```

#### Debounce（防抖）

防抖确保函数上次执行后的一段时间内，不会再次执行。当必须进行一些昂贵的计算来响应快速派发的事件时（比如鼠标滚动或键盘事件时），防抖是非常有用的。下面这个例子以250ms的延迟来改变文本输入。

```
import debounce from 'lodash.debounce';

class SearchBox extends React.Component {
  construtor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.emitChangeDebounced = throttle(this.emitChange, 250);
  }

  componentWillUnmount() {
    this.emitChangeDebounced.cancel();
  }

  render() {
    return (
      <input
        type="text"
        onChange={this.handleChange}
        placeholder="Search..."
        defaultValue={this.props.vlaue}
      />
    );
  }

  handleClick(e) {
    // React pools events, so wo read the value before debounce.
    // Alternately we could call `event.persist()` and pass the entire event.
    // For more info see reactjs.org/docs/events.html#event-pooling
    this.emitChangeDebounced(e.target.value);
  }

  emitChange(value) {
    this.props.onChange(value);
  }
}
```

#### requestAnimationFrame throttling

[requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)是在浏览器中排队等待执行的一种方法，它可以在呈现性能的最佳时间执行。一个函数被`requestAnimationFrame`放入队列后将会在下一帧触发。浏览器会努力确保每秒60帧（60fps）。然而，如果浏览器无法确保，那么自然会限制每秒的帧数。例如，某个设备可能只能处理每秒30帧，所以每秒只能得到30帧。使用`requestAnimationFrame`来节流是一种有用的技术，它可以防止在一秒中进行60帧以上的更新。如果要求一秒钟内完成100次更新，则会为浏览器创建额外的负担，而用户无法看到这些工作。

> 注意：
> 用这个方法时只能获取帧中最后发布的值。也可以在[MDN}(https://developer.mozilla.org/en-US/docs/Web/Events/scroll)中看优化的示例。


#### 测试你的速率限制

在测试速率限制的代码是否正确工作的时候，如果可以（对动画或操作）进行快进将会很有帮助。如果正在使用[jest](https://facebook.github.io/jest/)，那么可以使用[mock timers](https://facebook.github.io/jest/docs/en/timer-mocks.html)来快进。如果正在使用`requestAnimationFrame` 节流，那么就会发现[raf-stub](https://github.com/alexreardon/raf-stub)是一个控制动画帧的十分有用的工具。
