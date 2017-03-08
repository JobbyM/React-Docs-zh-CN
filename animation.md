> 此文章是翻译[Animation](https://facebook.github.io/react/docs/animation.html)这篇React（版本v15.4.0）官方文档。

## Animation Add-Ons

[ReactTransitionGroup](https://facebook.github.io/react/docs/animation.html#reacttransitiongroup) add-on component 是低级的动画API，[ReactCSSTransitionGroup](https://facebook.github.io/react/docs/animation.html#reactcsstransitiongroup) 是一个简单显示基本CSS 动画和变形的add-on component。

## High-level API: ReactCSSTransitionGroup

`ReactCSSTransitionGroup` 是一个基于 [ReactTransitionGroup](https://facebook.github.io/react/docs/animation.html#reacttransitiongroup) 的高级API并且当一个React component 进入或离开DOM 时很容易执行CSS 变形和动画。它的灵感来自优秀的[ng-animate](http://www.nganimate.org/) 库。

**Importing**

```jsx
import ReactCSSTranstionGroup from 'react-addons-css-transition-group' // ES6
var ReactCSSTranstionGroup = require('react-addons-css-transition-group') // ES5 with npm
var ReactCSSTranstionGroup = React.addons.CSSTransitionGroup; // ES5 with react-with-addons.js
```

```jsx
class TodoList extends Component {
  constructor(props) {
    super(props);
    this.state = {items: ['hello', 'world', 'click', 'me']};
    this.handleAdd = this.handleAdd.bind(this);
  }

  handleAdd() {
    const newItems = this.state.items.concat([
      prompt('Enter some text')
    ]);
    this.setState({items: newItems});
  }

  handleRemove(i) {
    let newItems = this.state.items.slice();
    newItems.splice(i, 1);
    this.setState({items: newItems});
  }

  render() {
    const items = this.state.items.map((item, i) => (
      <div key={item} onClick={() => this.handleRemove(i)}>
        {item}
      </div>
    ));

    return (
      <div>
        <button onClick={this.handleAdd}>Add Item</button>
        <ReactCSSTransitionGroup
          transitionName="example"
          transitionEnterTimeout={500}
          transitionLeaveTimeout={300}>
          {items}
        </ReactCSSTransitionGroup>
      </div>
    );
  }
}
```

>**Note：**
你必须为`ReactCSSTransitionGroup` 的所有子节点提供[the `key` attribute](https://facebook.github.io/react/docs/multiple-components.html#dynamic-children)，即使值渲染单独的一个条目。这是React 如何决定那一个孩子已经进入、离开或停留。

在component 中，当一个条目被添加到`ReactCSSTransitionGroup` ，它将会获得`example-enter` 这个CSS 类，而`example-enter-active` 这个CSS 类将会在下一次点击时被加入。这是一个基于`transitionName` prop 的规则。

你可以使用下面这些类去触发一个CSS 动画或变形。例如，尝试添这个CSS 并添加一个新的条目：
```css
.example-enter {
  opacity: 0.01;
}

.example-enter.example-enter-active {
  opacity: 1;
  transition: opacity 500ms ease-in;
}

.example-leave {
  opacity: 1;
}

.example-leave.example-leave-active {
  opacity: 0.01;
  transition: opacity 300ms ease-in;
}
```
你已经注意到动画持续时间需要在CSS 和渲染方法中这两个地方配置；这告诉React 什么时候从element 中移除动画类（animation class）-- 如果它正在离开--什么时候从DOM 中移除element。

### Animate Inital Mounting

`ReactCSSTransitionGroup` 提供一个可选的属性`transionAppear`，添加一个在首次加载component 时的额外变形阶段。在首次加载时通常没有变形阶段因为`transitionAppear` 默认值是`false`。下面这个例子传入的`transitionAppear` 的值为`true`。
```jsx
render() {
  return (
    <ReactCSSTransitionGroup
      transitionName="example"
      transitionAppear={true}
      transitionAppearTimeout={500}
      transitionEnter={false}
      transitionLeave={false}>
      <h1>Fading at Initial Mount</h1>
    </ReactCSSTransitionGroup>
  );
}
```
在首次加载`ReactCSSTransitionGroup` 期间将会得到`example-appear` 这个CSS 类，在下次点击时`example-appear-active` CSS 类被添加。
```css
.example-appear {
  opacity: 0.01;
}

.example-appear.example-appear-active {
  opacity: 1;
  transition: opacity .5s ease-in;
}
```
在首次加载是，`ReactCSSTransitionGroup` 的所有的子节点会得到`appear` 而不是`enter`。然后，之后所有的子节点都会添加到一个已经存在的`ReactCSSTransitionGroup` 中是`enter` 而不是`appear`。

>**Note：**
在`0.13` 版本中`transitionAppear` 被添加到`ReactCSSTransitionGroup`。为了保持向后兼容，默认值被设置为`false`。
然后，`transitionEnter` 和`transitionLeave` 的默认值是`true`因此默认你必须配置`transitionEnterTimeout` 和`transitionLeaveTimeout`。如果不需要进入和离开动画，传`transitionEnter={false}` 或者`transitionLeave={false}`。

### Custom  Classes

有可能会使用自定义类名为你的变形中的每一个步骤。替代传入一个字符串到变形名称（transitionName），你可以传入一个包含`enter` 和`leave` 类名的对象，或者这个对象包含`enter`，`enter-active`，`leave-active`和`leave` 类名。如果只有进入和离开类被提供，enter-active 和leave-active 类名将同追加`-active` 到类名之后来决定。下面是两个使用自定义类的例子：
```jsx
// ...
<ReactCSSTransitionGroup
  transitionName={ {
    enter: 'enter',
    enterActive: 'enterActive',
    leave: 'leave',
    leaveActive: 'leaveActive',
    appear: 'appear',
    appearActive: 'appearActive'
  } }>
  {item}
</ReactCSSTransitionGroup>

<ReactCSSTransitionGroup
  transitionName={ {
    enter: 'enter',
    leave: 'leave',
    appear: 'appear'
  } }>
  {item2}
</ReactCSSTransitionGroup>
// ...
```

### Animation Group Must Be Mounted To Work

为了将变形应用到孩子节点上，`ReactCSSTransitionGroup` 必须已经被加载到DOM 中或这`transitionAppear` 属性必须被设置为`true`。

下面这个例子将**不会**工作，因为`ReactCSSTransitionGroup` 正在随着新条目被加入，而不是这个新条目随着它被加载。对比这块和上面[Getting Started](https://facebook.github.io/react/docs/animation.html#getting-started) 去查看不同。
```jsx
render() {
  const items = this.state.items.map((item, i) => (
    <div key={item} onClick={() => this.handleRemove(i)}>
      <ReactCSSTransitionGroup transitionName="example">
        {item}
      </ReactCSSTransitionGroup>
    </div>
  ));

  return (
    <div>
      <button onClick={this.handleAdd}>Add Item</button>
      {items}
    </div>
  );
}
```

### Animating One or Zero Items

在上述例子中，我们渲染一个条目列表到`ReactCSSTransitionGroup`。然后，`ReactCSSTransitionGroup` 中的孩子可能会只有一个或零个条目。这会使得动画知道一个element 上进入或离开。同样的，你可以动画一个新element 替代当前的element。例如，我们可以实现像这样的一个图片旋转（image carousel ）：
```jsx
import ReactCSSTransitionGroup from 'react-addons-css-transition-group';

function ImageCarousel(props) {
  return (
    <div>
      <ReactCSSTransitionGroup
        transitionName="carousel"
        transitionEnterTimeout={300}
        transitionLeaveTimeout={300}>
        <img src={props.imageSrc} key={props.imageSrc} />
      </ReactCSSTransitionGroup>
    </div>
  );
}
```

### Disabling Animations

你可以使动画的`enter` 或`leave` 失效如果你想要。例如，有时你可能想要一个`enter` 动画并且没有`leave` 动画，但是`ReactCSSTransitionGroup` 等待一个动画完成在移除你的DOM 节点之前。你可以添加`transitionEnter={false}`或者`transitionLeave={false}`属性到`ReactCSSTransitionGroup`使这些动画失效。

>**Note：**
当使用`ReactCSSTransitionGroup` 时，这没有为你的component 进行提醒到变形已经结束或者去执行任何更多的复杂逻辑在动画上。如果你想要更多粒度的（fine-grained）控制，你可以使用低级的`ReactTransitionGroup` API 去提供你需要自定义变形的钩子（hook）。

## Low-level API: ReactTransitionGroup

**Importing**

```jsx
import ReactTransitionGroup from 'react-addons-transition-group' // ES6
var ReactTransitionGroup = require('react-addons-transition-group') // ES5 with npm
var ReactTransitionGroup = React.addons.TransitionGroup; // ES5 with react-with-addons.js
```

`ReactTransitionGroup` 是基本的动画。当孩子节点被声明式的添加或移除（就像[上例 ](https://facebook.github.io/react/docs/animation.html#getting-started)），配置在它上米昂的生命周期钩子（lifecycle hook）就会被调用。

* [componentWillAppear()](https://facebook.github.io/react/docs/animation.html#componentwillappear)
* [componentDidAppear()](https://facebook.github.io/react/docs/animation.html#componentdidappear)
* [componentWillEnter()](https://facebook.github.io/react/docs/animation.html#componentwillenter)
* [componentDidEnter()](https://facebook.github.io/react/docs/animation.html#componentdidenter)
* [componentWillLeave()](https://facebook.github.io/react/docs/animation.html#componentwillleave)
* [componentDidLeave()](https://facebook.github.io/react/docs/animation.html#componentDidleave)


### Rendering a Different Component

`ReactTransitionGroup` 默认渲染成一个`span`。你可以通过提供一个`component` 属性来改变这个默认行为。例如，这里你将渲染成一个`<ul>`：
```jsx
<ReactTransitionGroup component="ul">
  {/* ... */}
</ReactTransitionGroup>
```
任何额外的、用户自定义的、属性都会称为已经渲染的component 上的属性。例如，这里你讲渲染一个带CSS 类型的`<ul>`：
```jsx
<ReactTransitionGroup component="ul" className="animated-list">
  {/* ... */}
</ReactTransitionGroup>
```
每一个React 可以渲染的DOM component 都是可供使用的。然而，`component` 必须要成为一个DOM component。它可以是你想要的任何React component；甚至每一个你都可以自己写！写一个`component={List}` ，你的component 将会收到`this.props.children`。

### Rendering a Single Child


人们经常使用`ReactTransitionGroup` 去动画加载并且卸载单个子节点就像一个可拆卸的面板（collapsible panel）。通常`ReactTransitionGroup` 包裹所有的子节点在一个`span` 中（或者像上面描述的一个自定义`component` 中）。这是因为任何React comoennt 必须返回单个根element，`ReactTranstionGroup` 对这个规则也不例外。

然而如果你需要在`ReactTransitionGroup` 中渲染单个子节点，你可以完全避免把它包裹在一个`<span>` 或任何DOM component 中。为了实现它，创建一个自定义component 去直接渲染传入它的第一个子节点：
```jsx
function FirstChild(props) {
  const childrenArray = React.Children.toArray(props.children);
  return childrenArray[0] || null;
}
```
现在你可以配置`FirstChild` 作为`<ReactTransitionGroup>` 的`component` 属性，避免在最终DOM 中有任何包裹。
```jsx
<ReactTransitionGroup component={FirstChild}>
  {someCondition ? <MyComponent /> : null}
</ReactTransitionGroup>
```
这只在你对单个孩子节点进入或出时工作，就像一个可拆卸的面板（collapsible panel）。当动画多个子节点或使用另一个子节点替换单个子节点，这种方式不会工作，就像一个一个图片轮播（an image carousel）。对于一个图片轮播，当当前图片正在动画出去是，另一个图片将要动画进入，导致`<ReactTransitionGroup>` 需要给它们一个通用的DOM 父节点。你不能为多个子节点避免包裹器，但是你可以按照上面的描述使用`component` 自定义包裹器。

## Reference

### componentWillAppear()

```jsx
componentWillAppear(callback)
```
在一个`TransitionGroup` 中，初始化加载一个component 的`componentDidMount()` 方法的同时，这个方法被调用。它将阻止其它的动画发生直到`callback` 被调用。它只在`TransitionGroup` 的初次渲染中被调用。

### componentDidAppear()

```jsx
componentDidAppear()
```
当传入`componentWillAppear` 中的`callback` 函数被调用之后，这个方法才被调用。

### componentWillEnter()

```jsx
componentWillEnter(callback)
```
这个方法同已经被添加到`TransitionGroup` 中的component 的`componentDidMount()` 同时被调用。它将会阻止其它的动画反生，直到`callback` 被调用。它不会在`TransitionGroup` 的初次渲染时调用。


### componentDidEnter()

```jsx
componentDidEnter()
```
当传入[componentWillEnter()](https://facebook.github.io/react/docs/animation.html#componentwillenter) 中的`callback` 函数被调用之后，这个方法才被调用。

### componentWillLeave()

```jsx
componentWillLeave(callback)
```
当孩子节点已经从`ReactTransitionGroup` 中被移除后才会被调用。虽然孩子节点已经被移除了，`ReactTransitionGroup` 仍然将其保留在DOM 中，直到`callback` 被调用

### componentDidLeave()

```jsx
componentDidLeave()
```
当`willLeave` `callback` 被调用时（和`componentWillUnmount()` 同时） ，它才被调用。
