> 此文章是翻译[Web Components](https://facebook.github.io/react/docs/web-components.html)这篇React（版本v15.5.4）官方文档。

## Web Components

React 和[Web Component](https://developer.mozilla.org/en-US/docs/Web/Web_Components)分别用来构建解决不同的问题。Web Component 提供了强大的可复用component 的封装，React 提供了一个声明式库来保持DOM 和你的数据的同步。这两个目标是互补的。作为一个开发者，你可以自由的在你的Web Components 中使用React，或者在React 中使用Web Components，后者两者同时使用。

大多数使用React 的人不使用Web Component，但是如果你想要，尤其是使用Web Components 写的第三方UI components。

###  Using Web Components in React
```jsx
class HelloMessage extends React.Component {
  render(){
    return <div>Hello <x-search>{this.props.name}</x-search></div>
  }
}
```

>**Note：**
Web Components 通常提供一个命令式（imperative）API。例如，一个`video` Web Component 可能暴露`play()`和`pause()` 方法。为了访问Web Component 的命令式API，你需要使用一个ref 同DOM 节点直接交互。如果你正在使用第三方的Web Components，最好的解决方案是写一个React component 作为Web Component 的包装器（wrapper）。
通过Web Component 发出的事件不能正确的在React 渲染树中冒泡。你需要手动的在你的React component 中绑定这些事件句柄去控制这些事件。

一个常见的困惑是在Web Components 中使用“class” 而不是“className”。
```jsx
function BrickFlipbox(){
  return (
    <brick-flipbox class="demo" >
      <div>front<div>
      <div>back</div>
    </brick-flipbox>
  )
}
```

### Using React in your Web Components
```jsx
const proto = Object.create(HTMLElement.prototype, {
  attachedCallback: {
    value: function() {
      const mountPoint = document.createElement('span');
      this.createShadowRoot().appendChild(mountPoint);

      const name = this.getAttribute('name');
      const url = 'https://www.google.com/search?q=' + encodeURIComponent(name);
      ReactDOM.render(<a href={url}>{name}</a>, mountPoint);
    }
  }
});
document.registerElement('x-search', {prototype: proto});
```
