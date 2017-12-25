> 此文章是翻译[Rendering Elements](https://reactjs.org/docs/rendering-elements.html)这篇React（版本v16.2.0）官方文档。

## Rendering Elements

元素是构建React app 的最小块。

一个element 描述了你想要在屏幕上看到的东西：

```
const element = <h1>Hello, world</h1>;
```

不像浏览器DOM 元素，React 元素是纯对象，非常容易创建。React DOM 关心更新DOM 来匹配React 元素。

>**注意：**

>元素可能和另一个广为人知的概念组件产生混淆。我们将要在[下一章节](https://reactjs.org/docs/components-and-props.html)介绍组件。组件是由元素组成，我们建议你先阅读这部分再跳到下一章。

### Rendering an Element into the DOM

假如在你的HTML 文件的某个位置有一个`<div>`

```
<div id="root"></div>
```

我们称它为“root”DOM 节点，因为所有的在此节点中的内容都被React DOM 管理。

仅被React 构建的应用通常只有一个root DOM 节点。如果你正在集成React 到一个已经存在的应用，你可以按照你的喜好有多个独立的root DOM 节点。

为了渲染一个React 元素到一个root DOM 节点，需要将其都传入`ReactDOM.render()`：

```
const element = <h1>Hello, world</h1>;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

[在CodePen 上尝试](http://codepen.io/gaearon/pen/rrpgNB?editors=1010)

它将会在页面上显示“Hello World”。

### Updating the Rendered Element

React 元素是[不可变的](https://en.wikipedia.org/wiki/Immutable_object)。一旦你创建一个元素，你不能改变它的子节点或者特性。一个元素就像电影中的一帧：它代表某个时间点的UI 。

到目前为止，我们了解唯一更新UI 的方式是再创建一个元素，并将它传入`ReactDOM.render()` 方法。

考虑这个时钟的例子：

```
function tick() {
  const element = (
    <div>
      <h1>Hello, world</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

[在CodePen 上尝试](http://codepen.io/gaearon/pen/gwoJZk?editors=0010)

每个一秒通过`setInterval()` 回调函数来调用`ReactDOM.render()` 方法。

>**注意:**

>实际上，大多数React 应用只调用`ReactDOM.render()` 方法一次。下一章节我们将学习如何将现有代码封装到[stateful components](https://reactjs.org/docs/state-and-lifecycle.html)。

>我们建议你不要跳过主题因为它们建立在彼此之上。

### React Only Updates What's Necessary

React DOM 对比元素以及它的子节点同之前，并且只更新必要的DOM 将DOM 更新为理想状态。

你可以使用浏览器工具去验证[上一个例子](http://codepen.io/gaearon/pen/gwoJZk?editors=0010)：

![granular-dom-updates](img/granular-dom-updates.gif)

即使我们在每一秒都创建一个元素来描述整个UI 树，但是通过React DOM 我们仅更新文本节点。

在我们的经验中，思考UI 在每一个节点看上去是怎样的而不是随着时间去改变UI以此消除了一系列bug。
