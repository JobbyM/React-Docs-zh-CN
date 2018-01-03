> 此文章是翻译[CDN Links](https://reactjs.org/docs/cdn-links.html)这篇React（版本v16.2.0）官方文档。

## CDN Links

React 和ReactDOM 的UMD 构建通过CDN 可用。

```
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
```

上面的版本只打算为开发，并不适合生产。最小的和优化的生产版本React 在下面：

```
<script crossorigin src="https://unpkg.com/react@16/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js"></script>
```

为了加载一个`react` 和`react-dom`明确的版本，使用版本号替换`16`。

**Why the crossorigin Attribute?**

如果你从一个CDN 提供React 服务，我们建议保持`crossorigin` 特性设置：

```
<script crossorigin src="..."></script>
```

我们还建议验证你正在使用的CDN 设置`Access-Control-Allow-Origin: *` HTTP报头：
![cdn-cors-header](img/cdn-cors-header.png)

这使得在React 16 版本之后有一个更好的[错误控制体验](https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html)。
