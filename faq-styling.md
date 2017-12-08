> 此文章是翻译[Styling and CSS](https://reactjs.org/docs/faq-styling.html)这篇React（版本v16.2.0）官方文档。

## Styling and CSS

### 我如何在组件中添加CSS 类？

传入一个字符串作为`className` 的属性：

```
render() {
  return <span className="menu navigation-menu">Menu</span>
}
```

依赖组件的props 或state 对于CSS 类是常见的：

```
render() {
  let className = 'menu';
  if (this.props.isActive) {
    className += ' menu-active';
  }
  return <span className={className}></span>
}
```

如果你发现经常写这样的代码，[classnames](https://www.npmjs.com/package/classnames) 包可以简化它。

### 我可以使用行内样式吗？

可以，看[这篇](https://doc.react-china.org/docs/dom-elements.html#style) 关于样式的文档


### 行内样式有坏处吗？

CSS 类通常比行内样式更有效。

### 什么是CSS-in-JS？

“CSS-in-JS” 指的是CSS 的组成由JavaScript 而不是在外部文件中定义的模式。阅读[这里](https://github.com/MicheleBertoli/css-in-js)CSS-in-JS库的比较。

请注意，此功能不是React 的一部分，而是由第三方库提供的。React 没有关于样式定义的看法；如果有疑问，一个良好的起点是在一个单独的`*.css` 中定义你的样式。通常指使用`className` 来引用它们。

### 我可以在React 中实现动画吗？

React 可以用来增强动画。参考[React Transition Group](https://reactcommunity.org/react-transition-group/) 和[React Motion](https://github.com/chenglou/react-motion)
