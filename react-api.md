> 此文章是翻译[React Top-Level API](https://facebook.github.io/react/docs/react-api.html)这篇React（版本v15.4.0）官方文档。

## React Top-Level API

`React` 是React 库的入口。如果你用script 标签来使用React，这些顶级API 都在`React` 这个全局变量上。如果你在npm  下使用ES6，你可以这样写`import React from 'react'`。如果你在npm 下使用ES5，你可以这样写`var React = require('react')`。

## Overview

### Components

React components 让你将UI 分成独立的、可复用的块，并且独立的思考每一块。React comoponents 可以被`React.Component` 和 `React.PureComponent`子类定义。

* [React.Component](https://facebook.github.io/react/docs/react-api.html#react.component)
* [React.PureComponent](https://facebook.github.io/react/docs/react-api.html#react.purecomponent)


如果你不使用ES6 classes，你也可以使用下面这个帮助类（helper）来替代。

* [createClass()](https://facebook.github.io/react/docs/react-api.html#createclass)


### Creating React Elements

我们建议[使用JSX](https://facebook.github.io/react/docs/introducing-jsx.html)去描述你的UI 应该是什么样。每一个JSX element 都是调用`React.createElement()` 的语法糖。如果你使用JSX，你不必显示地调用下面的方法。

* [createElement()](https://facebook.github.io/react/docs/react-api.html#createelement)
* [createFactory()](https://facebook.github.io/react/docs/react-api.html#createfactory)


参考[Using React Without JSX](https://facebook.github.io/react/docs/react-without-jsx.html) 获取更多信息。

### Transforming Elements

`React` 也提供一些其他APIs：

* [cloneElement()](https://facebook.github.io/react/docs/react-api.html#cloneelement)
* [isValidElement()](https://facebook.github.io/react/docs/react-api.html#isvalidelement)
* [React.Children](https://facebook.github.io/react/docs/react-api.html#react.children)


### Typechecking with PropTypes

你可以使用`React.PropTypes` 来为component 的props 做类型检测。

* [React.PropTypes](https://facebook.github.io/react/docs/react-api.html#react.proptypes)
* [React.PropTypes.array](https://facebook.github.io/react/docs/react-api.html#react.proptypes.array)
* [React.PropTypes.bool](https://facebook.github.io/react/docs/react-api.html#react.proptypes.bool)
* [React.PropTypes.func](https://facebook.github.io/react/docs/react-api.html#react.proptypes.func)
* [React.PropTypes. number](https://facebook.github.io/react/docs/react-api.html#react.proptypes.number)
* [React.PropTypes.object](https://facebook.github.io/react/docs/react-api.html#react.proptypes.object)
* [React.PropTypes.string](https://facebook.github.io/react/docs/react-api.html#react.proptypes.string)
* [React.PropTypes.symbol](https://facebook.github.io/react/docs/react-api.html#react.proptypes.symbol)
* [React.PropTypes.node](https://facebook.github.io/react/docs/react-api.html#react.proptypes.node)
* [React.PropTypes.element](https://facebook.github.io/react/docs/react-api.html#react.proptypes.element)
* [React.PropTypes.instanceOf()](https://facebook.github.io/react/docs/react-api.html#react.proptypes.instanceof)
* [React.PropTypes.oneOf()](https://facebook.github.io/react/docs/react-api.html#react.proptypes.oneof)
* [React.PropTypes.oneOfType](https://facebook.github.io/react/docs/react-api.html#react.proptypes.oneoftype)
* [React.PropTypes.arrayOf](https://facebook.github.io/react/docs/react-api.html#react.proptypes.arrayof)
* [React.PropTypes.objectOf](https://facebook.github.io/react/docs/react-api.html#react.proptypes.objectof)
* [React.PropTypes.shape()](https://facebook.github.io/react/docs/react-api.html#react.proptypes.shape)
* [React.PropTypes.any](https://facebook.github.io/react/docs/react-api.html#react.proptypes.any)

验证器（Validator）对待props 默认是可选的。你可以使用`isReuqired` 去确认警告（warning）的显示如果prop 没有被提供。

* [isRequired](https://facebook.github.io/react/docs/react-api.html#isrequired)

### Add-Ons

如果你使用[react-with-addons.js](https://facebook.github.io/react/docs/addons.html)，React Add-Ons 将通过`React.addons` 来使用。

* [React.addons](https://facebook.github.io/react/docs/react-api.html#react.addons)

## Reference

### React.Component

`React.Component` 是React components 的基类（base class），当使用[ES6 classes](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 来定义。
```jsx
class Greeting extends Component {
  render(){
    return <h1>Hello, {this.props.name}</h1>
  }
}
```
查看[React.Component API Reference](https://facebook.github.io/react/docs/react-component.html) 获取基于`React.Component` 类的相关的一系列方法和属性（methods and properties）。

### React.PureComponent

`React.PureComponent` 是完全像[React.Component](https://facebook.github.io/react/docs/react-api.html#react.component)但是实现了使用浅比较props 和state 的[shouldComponentUpdate()](https://facebook.github.io/react/docs/react-component.html#shouldcomponentupdate) 方法。

如果你的React component 的`render()` 方法渲染通过props 和state 来获取相同的结果，在这种情况下你可以使用`React.PureComponent` 提升性能。

> **Note**
`React.PureComponent` 的`shouldComponentUpdate()` 只能浅比较对象（shallowly compares the objects）。如果包含复杂的数据结构，它可能产生false-negative 对深度不同（deeper differences）。只能将拥有简单的props 和state 的component 混合或者使用[foreUpdate()](https://facebook.github.io/react/docs/react-component.html#forceupdate) 方法，当你确定深度数据结构（deep data structures）发生改变。 或者使用[immuatable objects](https://facebook.github.io/immutable-js/) 帮助快速比较嵌套数据。
再者，`React.PureComponent` 的`shouldComponentUpdate()` 方法跳过了整个component 子树的props 更新。确认所有的孩子component 都是"pure"。

### createClass()

```jsx
React.createClass(specification)
```
如果你不实用ES6，你可以使用`React.createClass()` 帮助类来创建一个component 类。
```jsx
var Greeting = React.createClass({
  render: function(){
    return <h1>Hello, {this.props.name}</h1>
  }
})
```
参考[Using React Without ES6](https://facebook.github.io/react/docs/react-without-es6.html) 获取更多信息。

### createElement

```jsx
React.createElement(
  type,
  [props],
  [...children]
)
```
创建并返回一个新的给定类型的[React element](https://facebook.github.io/react/docs/rendering-elements.html)。这个类型参数要么是一个标签名字字符串（像`div` 或者是`span`），要么是一个[React component](https://facebook.github.io/react/docs/components-and-props.html) 类型（类或者是函数）。

`React.DOM` 提供了一个方便的包装为DOM component 的`React.createElement()`的方法。例如，`React.DOM.a(...)` 是一个`React.createElement('a',...)` 的方法包装。它们都被认为是合法的，并且我们孤立你使用JSX，而不是直接使用`React.createElement()`。

使用[JSX](https://facebook.github.io/react/docs/introducing-jsx.html) 编写的代码可以被转成使用`React.createElement()` 。你可以不必显示地直接、调用`React.createElement()`如果你使用JSX。参考[React Without JSX](https://facebook.github.io/react/docs/react-without-jsx.html) 获取更多信息。

### cloneElement()

```jsx
React.cloneElement(
  element,
  [props],
  [...children]
)
```
克隆并返回一个使用`element` 开始的的React element。这个将会有有一个原始的element 的props 和新props 浅合并的element。新的子节点将会替换已经存在的子节点。来自原始element 的`key` 和`ref`将会被保留。

`React.cloneElement()` 几乎等同于：
```jsx
<element.type {...element.props} {...props}>{children}</element.type>
```
无论如何，它也会保留`ref`。这意味着如果你通过它上面的`ref` 获取自己的子节点，你将不会有机会从你的祖先获取它。你只会获得绑定在你的新element 上相同的`ref`。

这个API  也可以作为`React.addons.cloneWithProps()` 废弃的替代。

### createFactory()

```jsx
React.createFactory(type)
```
返回一个生成指定类型的React elements的函数。像[React.createElement](https://facebook.github.io/react/docs/react-api.html#createElement)，这个类型参数要么是一个标签名字字符串（像`div` 或者是`span`），要么是一个[React component](https://facebook.github.io/react/docs/components-and-props.html) 类型（类或者是函数）。

这个帮助类被认为是合法的，我们滚里你要么使用JSX 恶如表示直接使用`React.createElement()`替代。

你将不必显示地直接调用`React.createFactory()`如果你使用JSX。参考[React Without JSX](https://facebook.github.io/react/docs/react-without-jsx.html) 获取更多信息。

### isValidElement()

```jsx
React.isValidElement(object)
```
验证对象是一个React element。返回`true` 或`false`。

### React.Children

`React.Children` 提供有助于处理`this.props.children` 这种不透明的数据结构。

### React.Children.map

```jsx
React.Children.map(children, function[(thisArg)])
```
在每一个直接子节点包含`children` 使用`this` 设置`thisArg`。如果`children` 是一个关键帧或数组，它将被改变：函数永远不会作为对象传入。如果节点是`null` 或`undefined`，返回一个`null` 或者是`undefined` 而不是一个数组。

### React.Chidren.forEach

```jsx
React.Children.forEach(children, function[(thisArg)])
```
像[React.Children.map()](https://facebook.github.io/react/docs/react-api.html#react.children.map)但是不会返回一个数组。


### React.Children.count

```jsx
Children.count(children)。
```
返回`children` 中的components 个数，等于传入`map` 或`forEach` 中回调函数的调用次数。

### React.Children.only

```jsx
React.Children.only(children)
```
返回`children` 中的唯一子节点。其它的抛出。

### React.Children.toArray

```jsx
React.Children.toArray(children)
```
返回`children` 不透明的数据结构作为平面数组其中键赋给每一个子节点。如果你想要在你的渲染方法中处理集合，尤其是你想要重新排序后者在向下传递之前将其分割`this.props.children`

> **Note：**
`React.Children.toArray` 改变键值去防止嵌套数组的语法当扁平化子节点列表。也就是，在返回数组中`toArray` 前置每一个键，所以每一个element 键的作用范围是包含它的输入数组。

### React.PropTypes

`React.PropTypes` 导出一系列的验证器它们可以在一个component 的`propTypes` 对象去验证被出入你的component 中的props。

获取关于`PropTypes` 的更多信息，请看[Typechecking with PropTypes](https://facebook.github.io/react/docs/typechecking-with-proptypes.html)。

### React.PropTypes.array

```jsx
React.PropTypes.array
```
验证prop 是一个原生的JavaScript 数组。

### React.PropTypes.bool

```jsx
React.PropTypes.bool
```
验证prop 是一个原生的JavaScript 布尔值。

### React.PropTypes.func

```jsx
React.PropTypes.func
```
验证prop 是一个JavaScript 函数。

### React.PropTypes.number

```jsx
React.PropTypes.number
```
验证prop 是一个原生的JavaScript 数值。

### React.PropTypes.object

```jsx
React.PropTypes.object
```
验证prop 是一个JavaScript 对象。

### React.PropTypes.string

```jsx
React.PropTypes.string
```
验证prop 是一个原生的JavaScript 字符串。

### React.PropTypes.symbol

```jsx
React.PropTypes.symbol
```
验证prop 是一个JavaScript symbol。

### React.PropTypes.node

```jsx
React.PropTypes.node
```
验证prop 是一个原生的JavaScript 数组。

### React.PropTypes.array

```jsx
React.PropTypes.array
```
验证prop 是一个任何可以被渲染的东西：数字，字符串，elements 或者是数组（片段）包含这些类型。

### React.PropTypes.element

```jsx
React.PropTypes.element
```
验证prop 是一个React element。

### React.PropTypes.instanceOf()

```jsx
React.PropTypes.instanceOf(class)
```
验证prop 是一个类的实例。使用JavaScript 的`instanceof` 操作符。

### React.PropTypes.oneOf()

```jsx
React.PropTypes.oneOf(arrayOfValue)
```
验证prop 被限制为一个特殊的值通过将其作为一个枚举。
```jsx
MyComponent.propTypes = {
     optionalEnum: React.PropTypes.oneOf(['News', 'Photos']),
}
```

### React.PropTypes.oneOfType()

```jsx
React.PropTypes.oneOfType(arrayOfPropTypes)
```
验证prop 是一个对象可以是许多类型中的一个。
```jsx
MyComponent.propTypes = {
  optionalUnion: React.PropTypes.oneOfType([
    React.PropTypes.string,
    React.PropTypes.number,
    React.PropTypes.instanceOf(Message)
  ]),
}

```

### React.PropTypes.arrayOf()

```jsx
React.PropTypes.arrayOf(propType)
```
验证prop 是一个确定类型的数组 。
```jsx
MyComponent.propTypes = {
  optionalArrayOf: React.PropTypes.arrayOf(React.PropTypes.number),
}
```

### React.PropTypes.objectOf()

```jsx
React.PropTypes.objectOf(propType)
```
验证prop 是一个具有某种类型属性值的对象。
```jsx
MyComponent.propTypes = {
  optionalObjectOf: React.PropTypes.objectOf(React.PropTypes.number),
}
```

### React.PropTypes.shape()

```jsx
React.PropTypes.shape(object)
```
验证prop 是一个特定形状的对象。
```jsx
MyComponent.propTypes = {
  optionalObjectWithShape: React.PropTypes.shape({
    color: React.PropTypes.string,
    fontSize: React.PropTypes.number
  }),
}
```

### React.PropTypes.any()

```jsx
React.PropTypes.any
```
验证prop 是一个任意数据类型的值。通过后面跟着`isRequired`
```jsx
MyComponent.propTypes = {
  requiredAny: React.PropTypes.any.isRequired,
}
```

### isRequired

```jsx
propType.isRequired
```
你可以使用`isRequired` 去链式上述任何验证器去确保警告显示如果prop 没有被提供。
```jsx
MyComponent.propTypes = {
  requiredFunc: React.PropTypes.func.isRequired,
}
```

### React.addons
```jsx
React.addons
```
当使用[react-with-addons.js](https://facebook.github.io/react/docs/addons.html)`React.addons` 导出一系列add-ons。
