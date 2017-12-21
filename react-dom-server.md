> 此文章是翻译[ReactDOMServer](https://reactjs.org/docs/react-dom-server.html)这篇React（版本v16.2.0）官方文档。

## ReactDOMServer

`ReactDOMServer` 对象允许你渲染组件为静态标签。通常，它被用在Node 服务器：

```
// ES modules
import ReactDOMServer from 'react-dom/server';
// CommonJS
var ReactDOMServer = require('react-dom/server');
```

### Overview

下面方法可以被用在服务器和浏览其环境：

* [renderToString()](https://reactjs.org/docs/react-dom-server.html#rendertostring)
* [renderToStaticMarkup()](https://reactjs.org/docs/react-dom-server.html#rendertostaticmarkup)


这些依赖于包（`stream`）的附加方法，**只在服务器端有效** 在浏览器中不工作。

* [renderToNodeStream()](https://reactjs.org/docs/react-dom-server.html#rendertonodestream)
* [renderToStaticNodeSream()](https://reactjs.org/docs/react-dom-server.html#rendertostaticnodestream)

## 参考

### renderToString()

```
ReactDOMServer.renderToString(element)
```

将React 元素渲染成初始的HTML。React 将返回一个HTML 字符串。你应该在服务器端使用这个方法生成HTML，并且在初始请求上发送标记以加快页面加载的，允许搜索引擎抓取你的网页来达到SEO（搜索引擎优化）目的。

如果你在一个已经在服务器端进行渲染的标记上的节点进行调用[ReactDOM.hydrate()](https://reactjs.org/docs/react-dom.html#hydrate)，React 将保留它并且只是绑定事件句柄，允许你有一个非常优秀的首次加载体验。

### renderToStaticMarkup()

```
ReactDOMServer.renderToStaticMarkup(element)
```

类似[renderToString](https://reactjs.org/docs/react-dom-server.html#rendertostring)，除了它不能创建额外的DOM 特性，React 在内部使用，例如`data-reactroot`。这是有用的如果你想要使用React 作为一个简单的静态页面生成器，因为剥离额外的属性可以节省大量的字节。

如果你计划在客户端使用React 生成标记交互，不要使用这个方法。相反在服务器端使用[renderToString](https://reactjs.org/docs/react-dom-server.html#rendertostring)，在客户端使用[ReactDOM.hydrate()](https://reactjs.org/docs/react-dom.html#hydrate)。


### renderToNodeStream()

```
ReactDOMServer.renderToNodeStream(element)
```

将React 元素渲染成初始的HTML。将返回一个[可读的流](https://nodejs.org/api/stream.html#stream_readable_streams) 来输出 HTML 字符串。这个流的HTML 输出恰好等于[ReactDOMServer.renderToString](https://reactjs.org/docs/react-dom-server.html#rendertostring) 的返回。你应该在服务器端使用这个方法生成HTML，并且在初始请求上发送标记以加快页面加载的，允许搜索引擎抓取你的网页来达到SEO（搜索引擎优化）目的。

如果你在一个已经在服务器端进行渲染的标记上的节点进行调用[ReactDOM.hydrate()](https://reactjs.org/docs/react-dom.html#hydrate)，React 将保留它并且只是绑定事件句柄，允许你有一个非常优秀的首次加载体验。

> 注意：

> 只服务器端。这个API 在浏览器端无效。

> 从这个方法返回的流将会返回utf-8 编码的字节流。如果你需要其它编码的流，可以看看[iconv-lite](https://www.npmjs.com/package/iconv-lite) 这个项目，它提供了流的编码转换。

### renderToStaticNodeStream

```
ReactDOM.renderToStaticNodeStream(element)
```

类似[renderToNodeStream](https://reactjs.org/docs/react-dom-server.html#rendertonodestream)，除了它不能创建额外的DOM 特性，React 在内部使用，例如`data-reactroot`。这是有用的如果你想要使用React 作为一个简单的静态页面生成器，因为剥离额外的属性可以节省大量的字节。

这个流的HTML 输出恰好等于[ReactDOMServer.renderToStaticMarkup](https://reactjs.org/docs/react-dom-server.html#rendertostaticmarkup) 的返回。

如果你计划在客户端使用React 生成标记交互，不要使用这个方法。相反在服务器端使用[renderToNodeStream](https://reactjs.org/docs/react-dom-server.html#rendertonodestream)，在客户端使用[ReactDOM.hydrate()](https://reactjs.org/docs/react-dom.html#hydrate)。

> 注意：

> 只服务器端。这个API 在浏览器端无效。

> 从这个方法返回的流将会返回utf-8 编码的字节流。如果你需要其它编码的流，可以看看[iconv-lite](https://www.npmjs.com/package/iconv-lite) 这个项目，它提供了流的编码转换。
