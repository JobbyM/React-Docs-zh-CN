> 此文章是翻译[ReactDOMServer](https://facebook.github.io/react/docs/react-dom-server.html)这篇React（版本v15.4.0）官方文档。

## ReactDOMServer

如果你用script 标签来使用React，这些顶级APIs 将会在全局`ReactDOMServer` 上有用。如果你在npm 中使用ES6，你可以写`import ReactDOMServer from 'react-dom/server'`。如果你在npm 中使用ES5，你可以写`var ReactDOMServer = require('react-dom/server')`。

## Overview

`ReactDOMServer` 允许你在服务端渲染你的component。

* [renderToString()](https://facebook.github.io/react/docs/react-dom-server.html#rendertostring)
* [renderToStaticMarkup()](https://facebook.github.io/react/docs/react-dom-server.html#rendertostaticmarkup)


## Reference

### renderToString()

```jsx
ReactDOMServer.renderToString(element)
```
渲染一个React element 去初始化HTML。这仅被应用在服务器端。React 将返回一个HTML 字符串。你应该在服务器端使用这个方法生成HTML并且将标记放在更快的页面加载的初始请求，允许搜索引擎抓取你的网页来达到SEO（搜索引擎优化）目的。

如果你在一个已经在服务器端进行渲染的标记上的节点进行调用[ReactDOM.render()](https://facebook.github.io/react/docs/react-dom.html#render)，React 将阻止它并且只是绑定事件句柄，允许你有一个首次加载的性能。

### renderToStaticMarkup()

```jsx
ReactDOMServer.renderToStaticMarkup(element)
```
类似[renderToString](https://facebook.github.io/react/docs/react-dom-server.html#rendertostring)，除了它保护创建额外的DOM 属性例如`data-reactid`，React 在内部使用。这是有用的如果你想要使用React 作为一个简单的静态页面生成器，因为剥离额外的属性可以节省大量的字节。
