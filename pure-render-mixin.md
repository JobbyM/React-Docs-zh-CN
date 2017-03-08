> 此文章是翻译[PureRenderMixin](https://facebook.github.io/react/docs/pure-render-mixin.html)这篇React（版本v15.4.0）官方文档。

## PureRenderMixin

>**Note：**
`PureRenderMixin` 混合早于`React.PureComponent` 。这篇参考文章是由于历史遗留问题提供的，你应该考虑使用[React.PureComponent](https://facebook.github.io/react/docs/component-api.html#react.purecomponent) 来替代。

如果你的React component 的渲染函数给定相同的props 和state 返回同一个结果，在某些情况下为了提示性能你应该使用混合。

例子：
```jsx
var PureRenderMixin = require('react-addons-pure-render-mixin');
React.createClass({
  mixins: [PureRenderMixin],

  render: function(){
    return <div className={this.props.className}>foo</div>
  }
})
```
使用ES6 class 语法的例子：
```jsx
import PureRenderMixin from 'react-addons-pure-render-=mixin';
class FooComponent extends React.Component {
  constructor(props){
    super(props)
    this.shouldComponentUpdate = PureRenderMixin.shouldComponentUpdate.bind(this)
  }

  render(){
    return <div className={this.props.className}>foo</div>
  }
}
```
在这个钩子下，混合实现了[shouldComponentUpdate]()，在这里它对比当前props 和state 同下一个，并且如果相等，返回`false`。

>**Note：**
这只是浅比较对象。如果这些包含复杂数据结构，它可能对深度不同产生漏报（false-negatives）。只对有简单的props 和state 的component 进行混合，或者使用`forceUpdate()`当你知道深度数据结构已经改变。或者考虑使用[immuatable objects](https://facebook.github.io/immutable-js/)去快速处理签到数据对比。
此外，`shouldComponentUpdate` 将会跳过整个component 子树的更新。确保所有的子compoennt 也是“纯的（pure）”。
