> 此文章是翻译[Web Components](https://reactjs.org/docs/web-components.html)这篇React（版本v16.2.0）官方文档。

## Web Components

React 和[Web 组件（Web Component）](https://developer.mozilla.org/en-US/docs/Web/Web_Components)分别用来构建解决不同的问题。Web 组件提供了强大的可复用组件的封装，React 提供了一个声明式库来保持DOM 和你的数据的同步。这两个目标是互补的。作为一个开发者，你可以自由的在你的Web 组件中使用React，或者在React 中使用Web 组件，后者两者同时使用。

大多数使用React 的人不使用Web 组件，但是如果你想要，尤其是使用Web 组件写的第三方UI 组件。

###  Using Web Components in React

```
class HelloMessage extends React.Component {
  render() {
    return <div>Hello <x-search>{this.props.name}</x-search>!</div>;
  }
}
```

>**注意：**

>Web 组件通常提供一个命令式（imperative）API。例如，一个`video` Web 组件可能暴露`play()`和`pause()` 方法。为了访问Web 组件的命令式API，你需要使用一个ref 同DOM 节点直接交互。如果你正在使用第三方的Web 组件，最好的解决方案是写一个React 组件作为Web 组件的包装器（wrapper）。

>通过Web 组件发出的事件不能正确的在React 渲染树中冒泡。你需要手动的在你的React 组件中绑定这些事件句柄去控制这些事件。

一个常见的困惑是在Web 组件中使用“class” 而不是“className”。

```
function BrickFlipbox() {
  return (
    <brick-flipbox class="demo">
      <div>front<div>
      <div>back</div>
    </brick-flipbox>
  );
}
```

### Using React in your Web Components

```
class XSearch extends HTMLElement {
  attachedCallback() {
    const mountPoint = document.createElement('span');
    this.attachShadow({ mode: 'open' }).appendChild(mountPoint);

    const name = this.getAttribute('name');
    const url = 'https://www.google.com/search?q=' + encodeURIComponent(name);
    ReactDOM.render(<a href={url}>{name}</a>, mountPoint);
  }
}
customElements.define('x-search', XSearch);
```

>**注意：**

>如果使用Babel 转换类，则此代码 **无效**。查看[这个问题](https://github.com/w3c/webcomponents/issues/587)的讨论。包括[custom-elements-es5-adapter](https://github.com/webcomponents/webcomponentsjs#custom-elements-es5-adapterjs)，在你加载你的Web 组件来解决这一问题之前。
