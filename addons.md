> 此文章是翻译[Add-Ons](https://facebook.github.io/react/docs/addons.html)这篇React（版本v15.4.0）官方文档。

## Add-Ons

React add-ons 是用于构建React 应用的有用的使用模块集合。**这些应该被认为是实验性的** 而且往往比核心更容易发生改变。

* [TransitionGroup 和CSSTransitionGroup](https://facebook.github.io/react/docs/animation.html)，用于处理不容易实现的动画和转换，例如在component 移除之前。
* [createFragment](https://facebook.github.io/react/docs/create-fragment.html) 创建一套额外帧的子节点。


下面这些add-ons 尽在React 开发版本中（没有缩小）：

* [Perl](https://facebook.github.io/react/docs/perf.html) 寻找优化机会的新能分析器。
* [ReactTestUtils](https://facebook.github.io/react/docs/test-utils.html)编写测试用例的简单助手。


### Legacy Add-ons

下面这些add-ons 是认为是历史遗留的（legacy），并且不建议使用。

* [PureRenderMixin](https://facebook.github.io/react/docs/pure-render-mixin.html)，使用[React.PureComponent](https://facebook.github.io/react/docs/react-api.html#react.purecomponent)替代。
* [shallowCompare](https://facebook.github.io/react/docs/shallow-compare.html)，是一个帮助函数用来在component 中执行props 和state 浅比较用来决定一个component 是否应该被更新。
* [update](https://facebook.github.io/react/docs/update.html)，使用[kolodny/immutability-helper](https://github.com/kolodny/immutability-helper)替代。


### Deprecated Add-ons

[LinkedStateMixin](https://facebook.github.io/react/docs/two-way-binding-helpers.html) 已经废弃了。

## Using React with Add-ons

如果使用npm，你可以独立地从npm 进行安装（例如，`npm install react-addons-test-utils`）并导入它们：
```jsx
import Perf from 'react-addons-perf'; // ES6
var Perf = require('react-addon-perf'); // ES5 with npm
```
如果使用CDN，你可以使用`react-with-addons.js`替代`react.js`：
```jsx
<script src="https://unpkg.com/react@15/dist/react-with-addons.js"></script>
```
add-ons 通过时全局`React.addons` 来使用（例如，`React.addons.TestUtils`）
