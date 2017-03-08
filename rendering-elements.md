> 此文章是翻译[Rendering Elements](https://facebook.github.io/react/docs/rendering-elements.html)这篇React（版本v15.4.0）官方文档。

## Rendering Elements

Elements 是构建React app 的最小块。

一个element 描述了你想要在屏幕上看到的东西：
```jsx
const element = <h1>Hello, world</h1>
```
不像浏览器DOM elements，React elements 是纯对象，非常容易创建。React DOM 关心更新DOM 来匹配React elements。

>**Note：**
Elements 可能和另一个广为人知的概念components 产生混淆。我们将要在[下一章节](https://facebook.github.io/react/docs/components-and-props.html)介绍components。Components 是有elements 组成，我们建议你先阅读这部分再跳到下一章。

### Rendering an Element into the DOM

我们说在你的HTML 文件的某个位置有一个`<div>`
```html
<div id="root"></div>
````
我们称它为“root”DOM 节点因为所有的此节点中的内容都被React DOM 管理。

仅被React 构建的应用通常只有一个root DOM 节点。如果你正在集合React 到一个已经存在的应用，你可以按照你的喜好有多个独立的root DOM 节点。

为了渲染一个React element 到一个root DOM 节点，需要将其都传入`ReactDOM.render()`：
```jsx
const element = <h1>Hello, world</h1>

ReactDOM.render(
  element,
  document.getElementById('root')
)
```
它将会在页面上显示“Hello World”。

### Updating the Rendered Element

React elements 是[不可变的](https://en.wikipedia.org/wiki/Immutable_object)。一旦你创建一个element，你不能改变它的子节点以及attribute。一个element 就像电影中的一帧：它代表某个时间点的UI 。

到目前为止，我们了解唯一更新UI 的方式是再创建一个element，并将它传入`ReactDOM.render()` 方法。

考虑这个时钟的例子：
```jsx
function tick(){
  const element = (
    <div>
      <h1>Hello, world</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  )
  ReactDOM.render(
    element,
    document.getElementById('root')
  )
}

setInterval(tick, 1000)
```
每个一秒通过`setInterval()` 回调函数来调用`ReactDOM.render()` 方法。

>**Note:**
实际上，多说React 应用只调用`ReactDOM.render()` 方法一次。下一章节我们将学习如何将有代码封装到[stateful components](https://facebook.github.io/react/docs/state-and-lifecycle.html)。
我们建议你不要跳过主题因为它们建立在彼此之上。

### React Only Updates What's Necessary

React DOM 对比elements 以及它的子节点同之前，并且只更新必要的DOM 的理想状态。

你可以使用浏览器工具去验证[上一个例子](http://codepen.io/gaearon/pen/gwoJZk?editors=0010)：

![granular-dom-updates](img/granular-dom-updates.gif)

即使我们在每一秒都创建一个element 来描述整个UI 树，但是通过React DOM 我们仅更新文本节点。

在我们的经验中，思考UI在每一个节点看上去是怎样的而不是随着时间去改变UI以此消除了一系列bug。
