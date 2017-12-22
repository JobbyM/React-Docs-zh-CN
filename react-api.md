> 此文章是翻译[React Top-Level API](https://reactjs.org/docs/react-api.html)这篇React（版本v16.2.0）官方文档。

## React Top-Level API

`React` 是React 库的入口。如果你用`<script>` 标签来使用React，这些顶级API 都在`React` 这个全局变量上。如果你在npm 下使用ES6，你可以这样写`import React from 'react'`。如果你在npm 下使用ES5，你可以这样写`var React = require('react')`。

### Overview

#### Components

React 组件让你将UI 分成独立的、可复用的块，并且独立的思考每一块。React 组件可以被定义为`React.Component` 和 `React.PureComponent` 的子类。

* [React.Component](https://reactjs.org/docs/react-api.html#reactcomponent)
* [React.PureComponent](https://reactjs.org/docs/react-api.html#react.purecomponent)


如果你不使用ES6 类，你也可以使用下面这个`create-react-class`来替代。查看[Using React without ES6](https://reactjs.org/docs/react-without-es6.html)了解更多。

#### Creating React Elements

我们建议[使用JSX](https://reactjs.org//docs/introducing-jsx.html)去描述你的UI 应该是什么样。每一个JSX 元素都是调用[React.createElement()](https://reactjs.org/docs/react-api.html#createelement) 的语法糖。如果你使用JSX，你不必显示地调用下面的方法。

* [createElement()](https://reactjs.org/docs/react-api.html#createelement)
* [createFactory()](https://reactjs.org/docs/react-api.html#createfactory)


参考[Using React Without JSX](https://reactjs.org/docs/react-without-jsx.html) 获取更多信息。

#### Transforming Elements

`React` 也提供一些其他操作元素的API ：

* [cloneElement()](https://reactjs.org/docs/react-api.html#cloneelement)
* [isValidElement()](https://reactjs.org/docs/react-api.html#isvalidelement)
* [React.Children](https://reactjs.org/docs/react-api.html#react.children)

#### Fragments

React 还提供了一个组件，用于在没有包装器的情况下渲染多个元素。

* [React.Fragment](https://reactjs.org/docs/react-api.html#reactfragment)

## 参考

### React.Component

`React.Component` 是React 组件的基类，使用[ES6 classes](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 来定义。

```
class Greeting extends React.Component {
  render(){
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

查看[React.Component API Reference](https://reactjs.org/docs/react-component.html) 获取基于`React.Component` 类的相关的一系列方法和属性。

### React.PureComponent

`React.PureComponent` 类似于[React.Component](https://reactjs.org/docs/react-api.html#react.component)。它们两个的不同是，[React.Component](https://reactjs.org/docs/react-api.html#react.component) 没有实现[shouldComponentUpdate()](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)，但是`React.PureComponent` 但是使用浅比较props 和state 实现了它。

如果你的React 组件的`render()` 方法根据给定的相同的props 和state 渲染相同的结果，在这种情况下你可以使用`React.PureComponent` 提升性能。

>**注意**

>`React.PureComponent` 的`shouldComponentUpdate()` 只能浅比较对象（shallowly compares the objects）。如果包含复杂的数据结构，它可能产生false-negative 对深度不同（deeper differences）。当你期望拥有简单的props 和state ，只能扩展`PureComponent`，或者使用[foreUpdate()](https://reactjs.org/docs/react-component.html#forceupdate) 方法，当你确定深度数据结构（deep data structures）发生改变。 或者使用[immuatable objects](https://facebook.github.io/immutable-js/) 帮助快速比较嵌套数据。

>再者，`React.PureComponent` 的`shouldComponentUpdate()` 方法跳过了整个组件子树的props 更新。确认所有的孩子组件都是"pure"。

### createElement

```
React.createElement(
  type,
  [props],
  [...children]
)
```

创建并返回一个新的给定类型的[React 元素](https://reactjs.org/docs/rendering-elements.html)。这个类型参数要么是一个标签名字字符串（像`div` 或者是`span`），要么是一个[React 组件](https://reactjs.org/docs/components-and-props.html) 类型（类或者是函数），要么是一个[React fragment](https://reactjs.org/docs/react-api.html#reactfragment) 类型。

使用[JSX](https://reactjs.org/docs/introducing-jsx.html) 编写的代码可以被转成使用`React.createElement()` 。你可以不必显示地直接、调用`React.createElement()`如果你使用JSX。参考[React Without JSX](https://reactjs.org/docs/react-without-jsx.html) 获取更多信息。

### cloneElement()

```
React.cloneElement(
  element,
  [props],
  [...children]
)
```

克隆并返回一个使用`element` 开始的新的React 元素。这个将会有一个原始的元素的props 和新props 浅合并的元素。新的子节点将会替换已经存在的子节点。来自原始element 的`key` 和`ref`将会被保留。

`React.cloneElement()` 几乎等同于：

```
<element.type {...element.props} {...props}>{children}</element.type>
```

无论如何，它也会保留`ref`。这意味着如果你通过它上面的`ref` 获取自己的子节点，你将不会有机会从你的祖先获取它。你只会获得绑定在你的新元素的相同的`ref`。

这个API 也可以作为`React.addons.cloneWithProps()` 废弃的替代。

### createFactory()

```
React.createFactory(type)
```

返回一个生成指定类型的React 元素的函数。像[React.createElement](https://reactjs.org/docs/react-api.html#createElement)，这个类型参数要么是一个标签名字字符串（像`div` 或者是`span`），要么是一个[React 组件](https://reactjs.org/docs/components-and-props.html) 类型（类或者是函数），要么是一个[React fragment](https://reactjs.org/docs/react-api.html#reactfragment) 类型。

这个帮助类被认为是历史遗留的，我们鼓励你要么使用JSX ，要么直接使用`React.createElement()`替代。

你将不必显示地直接调用`React.createFactory()`如果你使用JSX。参考[React Without JSX](https://reactjs.org/docs/react-without-jsx.html) 获取更多信息。

### isValidElement()

```
React.isValidElement(object)
```

验证对象是一个React 元素。返回`true` 或`false`。

### React.Children

`React.Children` 提供有助于处理`this.props.children` 这种不透明的数据结构。

#### React.Children.map

```
React.Children.map(children, function[(thisArg)])
```

在每一个直接子节点包含`children` 使用`this` 设置`thisArg`。如果`children` 是一个关键帧或数组，它将被遍历：该函数永远不会传递容器对象。如果节点是`null` 或`undefined`，返回一个`null` 或者是`undefined` 而不是一个数组。

#### React.Chidren.forEach

```
React.Children.forEach(children, function[(thisArg)])
```

像[React.Children.map()](https://reactjs.org/docs/react-api.html#react.children.map)但是不会返回一个数组。

#### React.Children.count

```
React.Children.count(children)。
```

返回`children` 中的组件个数，等于传入`map` 或`forEach` 中回调函数的调用次数。

#### React.Children.only

```
React.Children.only(children)
```

验证`children` 有且仅有一个子节点。否则这个方法抛出一个错误。


>**注意：**

>`React.Children.only` 不接受[React.Children.map()](https://reactjs.org/docs/react-api.html#reactchildrenmap) 的返回值，因为它是一个数组而不是一个React 元素。

#### React.Children.toArray

```
React.Children.toArray(children)
```

返回`children` 不透明的数据结构作为平面数组其中键赋给每一个子节点。如果你想要在你的渲染方法中处理子节点集合，尤其是你想要重新排序后者在向下传递之前将其分割`this.props.children`

>**注意：**

>`React.Children.toArray` 改变键值去防止嵌套数组的语法当扁平化子节点列表。也就是，在返回数组中`toArray` 前置每一个键，所以每一个元素键的被限定到包含它的输入数组。

#### React.Fragment

在不创建附加DOM 元素的情况下，`React.Fragment` 组件允许你在`render()` 方法中返回多个元素：

```
render() {
  return (
    <React.Fragment>
      Some text.
      <h2>A heading</h2>
    </React.Fragment>
  );
}
```

你也可以使用`<></>` 缩写语法。更多信息，查看[React v16.2.0:Improved Support for Fragments](https://reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html)
