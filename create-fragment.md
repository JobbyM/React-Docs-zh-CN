> 此文章是翻译[Keyed Fragments](https://facebook.github.io/react/docs/create-fragment.html)这篇React（版本v15.4.0）官方文档。

## Keyed Fragments

**Importing**

```jsx
import createFragment from 'react-addons-create-fragment' // ES6
var createFragment = require('react-addons-create-fragment') // ES5 with npm
var createFragment = React.addons.createFragment; // ES5 with react-with-addons.js
```

## Overview

在大多数情况下，你使用`key` 属性去配置你从`render` 中返回的每一个element 上的key。然而，在一种情况下会被破坏：如果你有两套子节点需要重新排序，在不添加包裹器element 的情况下，没有办法在每一个上去设置key。

也就是说，你有这样的一个component：
```jsx
function Swapper(props) {
  let children;
  if (props.swapped) {
    children = [props.rightChildren, props.leftChildren];
  } else {
    children = [props.leftChildren, props.rightChildren];
  }
  return <div>{children}</div>;
}
```
当你改变`swapped` 属性时，孩子节点将会被卸载然后再重新加载，因为因为这里没有任何key 标志在这两套孩子节点上。

为了解决这个问题，你可以使用`createFragment` add-ons 去给每一套孩子节点设置key。

**Array<ReactNode> createFragment(object children) #**

替代创建数组，我们这样写：
```jsx
import createFragment from 'react-addons-create-fragment'

function Swapper(props) {
  let children;
  if (props.swapped) {
    children = createFragment({
      right: props.rightChildren,
      left: props.leftChildren
    });
  } else {
    children = createFragment({
      left: props.leftChildren,
      right: props.rightChildren
    });
  }
  return <div>{children}</div>;
}
```

传入到对象（也就是，`left` 和`right`）中的key 是作为整套孩子节点的key，对象key 的顺序被用来决定渲染孩子节点的顺序。通过这个改变，这两套孩子节点将会正确地在DOM 中重新排序，而不需要卸载。

`createFragment` 的返回值应该是作为一个不透明的对象；你可以使用[React.Children](https://facebook.github.io/react/docs/react-api.html#react.children) 助手去遍历一个fragment，但是不能直接访问它。注意，我们依赖JavaScript 引擎保护对象枚举的秩序，而不是由所有主流浏览器实现的规格（spec）保证，虚拟机（VMs）中是非数字键（non-numeric key）的对象。
