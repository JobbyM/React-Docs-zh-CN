> 此文章是翻译[ReactDOM](https://reactjs.org/docs/react-dom.html)这篇React（版本v16.2.0）官方文档。

## ReactDOM

如果你从`<script>` 标签来加载React，这些顶级APIs 将会在全局`ReactDOM` 上。如果你在npm 中使用ES6，你可以写`import ReactDOM from 'react-dom'`。如果你在npm 中使用ES5，你可以写`var ReactDOM = require('react-dom')`。

### Overview

`react-dom` 包提供的DOM-specific 方法可以在你的应用中顶层中使用，并且作为一个逃生舱（an escape hatch），如果你需要获取React 模型外。大多数你的组件应该不需要使用这个模块。


* [render()](https://reactjs.org/docs/react-dom.html#render)
* [hydreate()](https://reactjs.org/docs/react-dom.html#hydrate)
* [unmountComponentAtNode()](https://reactjs.org/docs/react-dom.html#unmountcomponentatnode)
* [findDOMNode()](https://reactjs.org/docs/react-dom.html#finddomnode)
* [createPortal()](https://reactjs.org/docs/react-dom.html#createportal)

### Browser Support

React 支持所有的流行浏览器，包括IE9 及以上。虽然旧浏览器[需要一些polyfills](https://reactjs.org/docs/javascript-environment-requirements.html)。

> **注意**
我们不支持老版本的不支持ES5 方法的浏览器，但是你发现如果在页面中使用了[es5-shim and es5-sham](https://github.com/es-shims/es5-shim) 这些polyfill，你的应用在旧版本浏览器中仍然可以运行。如果你选择走这条路，你就会自己动手。

## 参考

### render()

```
ReactDOM.render(element,container[, callback])
```

渲染一个React 元素到由`container` 提供的DOM 中，并且返回一个组件的[reference](https://reactjs.org/docs/more-about-refs.html)（或者对[stateless components](https://reactjs.org/docs/components-and-props.html#functional-and-class-components) 来说返回`null`）。

如果React 元素之前已经渲染到`container` 中，那么它将对其进行更新，并且只有根据需要对DOM 进行突变以反映最新的React 元素。

如果可选的回调函数被提供，它将在组件渲染之后或更新之后执行。

>**注意：**

>`ReactDOM.render()` 控制你传入容器节点（container node）的内容。当第一次调用时，任何已经存在的DOM 元素都会被替换。之后使用React 的DOM diffing algorithm 来进行有效的更新。

>`ReactDOM.render()` 不会修改容器节点（只是修改容器的子节点）。它可能插入一个组件到已存在的DOM 节点中而不会覆盖已经存在的子节点。

>`ReactDOM.render()` 当前返回根`ReactComponent` 实例的引用。无论如何，使用它返回值是历史遗留的并且应该被避免，因为React 的未来的版本在某些情况下可能会异步渲染组件。如果你需要根`ReactComponent` 实例的引用，更好的解决方式是绑定一个[callback ref](https://reactjs.org/docs/more-about-refs.html#the-ref-callback-attribute) 到根元素（root element）上。

>使用`ReactDOM.render()` 去混合一个服务器端容器是被废弃，将会在React 17 中被移除。使用[hydrate()](https://reactjs.org/docs/react-dom.html#hydrate) 替代。

### hydrate()

```
ReactDOM.hydrate(element, container[, callback])
```

与[render()](https://reactjs.org/docs/react-dom.html#render)相同，但用于对其HTML内容由[ReactDomServer](https://reactjs.org/docs/react-dom-server.html) 渲染的容器进行混合。React 将尝试将事件侦听器绑定到现有标记。

React 预期在服务器和客户端之间渲染的内容是相同的。它可以修补文本内容中的差异，但你应该将不匹配视为错误并修复它们。在开发模式中，React 警告混合过程中的不匹配。在不匹配的情况下，不存在将会修补特性差异的保证。这对于性能原因很重要，因为在大多数应用程序中，不匹配是罕见的，因此验证所有标记将会非常昂贵。

如果单个元素的特性或文本内容在服务器和客户端之间不可避免地不同(例如，时间戳)，则可以通过向元素中添加`suppressHydrationWaring={true}` 来消除警告。它只工作一个层次的深度，并打算作为一个逃生舱口。不要过度使用它。除非它是文本内容，否则React 仍然不会尝试修补它，因此在未来更新之前，它可能仍然不一致。

如果你有意在服务器和客户端上渲染不同的内容，则可以进行两遍渲染。在客户端上渲染不同的内容的组件可以读取类似`this.state.iClient` 的状态变量，你可以在`componentDidMount()` 中将其设置为`true`。这样，初始渲染过程将渲染与服务器相同的内容，避免了不匹配，但是在混合之后，附加的方法将同步发生。请注意，此方法将使你的组件变慢，因为它们必须渲染两次，因此请谨慎使用。

请记住，考虑到用户对慢速连接的体验。JavaScript 代码可能比初始HTML呈现更晚地加载，因此，如果在仅客户端过程中渲染不同的内容，则转换可以是不和谐的。然而，如果执行得很好，则在服务器上渲染应用程序的“外壳”可能是有益的，并且只显示客户端上的一些额外的小部件。要了解如何在不获取标记不匹配问题的情况下执行此操作，请参阅上一段中的说明。

### unmountComponentAtNode()

```
ReactDOM.unmountComponentAtNode(container)
```

从DOM 中移除一个已经加载的React 组件，并清理它的事件句柄和state。如果在容器中没有组件被加载，调用这个方法不会做任何事。如果组件被卸载将返回`true`，如果没有组件被卸载，将返回`false`。

### findDOMNode()

```
ReactDOM.findDOMNode(component)
```

如果组件已经被载入DOM，这将返回相应的原生浏览器DOM 元素。这个方法对读取DOM 外的值是有用的，就像表单域值以及执行DOM 分析。**在大多数情况下，你可以绑定一个ref 到DOM 节点上从而避免使用`findDOMNode`**。

当组件渲染成`null` 或`false`，`findDOMNode` 将会返回`null`。当组件渲染成字符串，`findDOMNode` 返回一个包含这个值的文本DOM 节点。从React 16开始，组件可以返回一个带有多个子节点的片段，在这种情况下，`findDOMNode` 将返回对应于第一个非空子节点的DOM节点。

>**注意：**

>`findDOMNode` 是一个逃生舱（escape hatch）被用来访问底层DOM 节点。在大多数情况下，逃生舱（escape hatch）的使用是被阻止的，因为它刺穿（pierce）组件的抽象。

>`findDOMNode` 只在已经加载的组件中使用（也就是，组件已经存在于DOM 中）。如果你尝试在还没有被加载的组件上调用就像（在还没有被创建component 的`render()` 方法中调用`findDOMNode()` ）将会抛出一个异常。

>`findDOMNode` 不能被用在函数组件中。

### createPortal()

```
ReactDOM.createPortal(child, container)
```

创建一个portal。Portal 提供一种方法去[渲染子组件到DOM 组件层级之外存在的DOM 节点上](https://reactjs.org/docs/portals.html)
