> 此文章是翻译[ReactDOM](https://facebook.github.io/react/docs/react-dom.html)这篇React（版本v15.4.0）官方文档。

## ReactDOM

如果你用script 标签来使用React，这些顶级APIs 将会在全局`ReactDOM` 上有用。如果你在npm 中使用ES6，你可以写`import ReactDOM from 'react-dom'`。如果你在npm 中使用ES5，你可以写`var ReactDOM = require('react-dom')`。

## Overview

`react-dom` 包提供的DOM-specific 方法可以在你的应用中顶层中使用，并且作为一个编码开口（an escape hatch），如果你需要获取React 模型外。大多数你的comoent 应该不需要使用这个模块。


* [render()](https://facebook.github.io/react/docs/react-dom.html#render)
* [unmountComponentAtNode()](https://facebook.github.io/react/docs/react-dom.html#unmountcomponentatnode)
* [findDOMNode()](https://facebook.github.io/react/docs/react-dom.html#finddomnode)


### Browser Support

React 支持所有的流行浏览器，包括IE9 以上。

> **Note**
我们不支持老版本的浏览器因为它们不支持ES5 方法，但是你发现如果在页面中使用了[es5-shim and es5-sham](https://github.com/es-shims/es5-shim) 这些polyfill，你的引用在旧版本浏览器中仍然可以运行。

## Reference

### render()

```jsx
ReactDOM.render(
  element,
  container,
  [callback]
)
```
渲染一个React element 到由`container` 提供的DOM 中，并且返回一个component 的[reference](https://facebook.github.io/react/docs/more-about-refs.html)（或者对[stateless components](https://facebook.github.io/react/docs/components-and-props.html#functional-and-class-components) 来说返回 null）。

如果React element 之前已经渲染到`container` 中，它将执行更新并且只是改变必须要的DOM 去响应最新的React element。

如果可选的回调函数被提供，它将在component 渲染之后或更新之后进行执行。

>**Note：**
`ReactDOM.render()` 控制你传入容器节点（container node）的内容。当第一次调用时，任何已经存在的DOM elements 都会被替换。之后使用React 的DOM diffing algorithm 来进行有效的更新。
`ReactDOM.render()`不会修改容器节点（只是修改容器的子节点）。它可能插入一个component 到已存在的DOM node 中而不会覆盖已经存在的子节点。
`ReactDOM.render()`当前返回`ReactComponent` 实例的引用。无论如何，使用它返回值是合法的但是应该被避免因为React 未来的版本在某些情况下可以异步渲染components。如果你需要根`ReactComponent` 实例的引用，更好的解决方式是绑定一个[callback ref](https://facebook.github.io/react/docs/more-about-refs.html#the-ref-callback-attribute) 到根元素（root element）上。

### unmountComponentAtNode()

```jsx
ReactDOM.unmountComponentAtNode(container)
```
从DOM 中移除一个已经卸载的（unmounted）React component并清理它的事件句柄和state。如果在容器中没有component 被卸载（unmounted），调用这个方法不会做任何事。如果component 被卸载将返回`true`，如果没有component 去卸载将返回`false`。

### findDOMNode()

```jsx
ReactDOM.findDOMNode(component)
```
如果component 已经被载入DOM，这将返回相应的原生浏览器DOM 元素。这个方法对读取DOM 外的值是有用的，就像表单域值（form field value）以及执行DOM 分析（measurement） 。在大多数情况下，你可以绑定一个ref 到DOM 节点上从而避免使用`findDOMNode`。当`render()` 返回`null` 或`false`，`findDOMNode` 将会返回`null`。

>**Note：**
`findDOMNode` 是一个编码入口（escape hatch）被用来反问底层DOM 节点。在大多数情况下，编码入口（escape hatch）的使用是被阻止的，因为它刺穿（pierce）component 的抽象。
`findDOMNode`只在已经加载的（mounted）component 中使用（那是，component 已经存在于DOM 中）。如果你尝试调用一个还没有被加载的component（像，在`render()` 方法中调用`findDOMNode()` 还没有被创建）将会抛出一个异常。
`findDOMNode` 不能被用在functional component 中。
