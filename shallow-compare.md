> 此文章是翻译[Shallow Compare](https://facebook.github.io/react/docs/shallow-compare.html)这篇React（版本v15.4.0）官方文档。

## Shallow Compare

>**注意：`shallowCompare` 是一个历史遗留的add-on。请使用[React.PureComponent](https://facebook.github.io/react/docs/react-api.html#react.purecomponent)来替代。**

**Importing**

```jsx
import shallowCompare from 'react-addons-shallow-compare' // ES6
var shallowCompare = require('react-addons-shallow-compare') // ES5 with npm
var shallowCompare = React.addons.shallowCompare; // ES5 with react-with-addon.js
```

## Overview

在[React.PureComponent](https://facebook.github.io/react/docs/react-api.html#react.purecomponent)被提出之前，`ShallowCompare`通常被用来实现同[PureRenderMixin](https://facebook.github.io/react/docs/pure-render-mixin.html)相关的功能，在React 中使用ES6 class 时。

如果你的React component 的渲染函数是“纯的（pure）”（换句话说，给定同样的props 和state 返回相同的结果），在某些情况下，你可以使用这个帮助函数提升性能。

例子：
```jsx
export class SampleComponent extends React.Component {
  shouldComponentUpdate(nextPros, nextState){
    return shallowCompare(this, nextPros, nextState)
  }

  render(){
    return <div className={this.props.className}>foo</div>
  }
}
```
`shallowCompare` 执行一个浅的相等性检查在当前`props` 和`nextProps` 同样在当前`state` 和`nextState` 对象上。

它通过遍历正在对比对象的key 执行它，当每一个对象中的key 的value 不是严格相等（strict equal）返回true。

`shallowCompare` 返回`true` 如果对props 或者state 的浅对比失败，因此component 将进行更新。

`shallowCompare` 返回`false` 如果对props 或者state 的浅对比通过，因此component 不需要更新。
