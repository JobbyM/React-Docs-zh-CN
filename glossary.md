> 此文章是翻译[Glossary](https://reactjs.org/docs/glossary.html)这篇React（版本v16.2.0）官方文档。

## Glossary of React Terms

### 单页应用（Single-page Application）

单页应用程序是一个应用，它加载应用程序所需的单个HTML页面和所有必需的资源（如JavaScript和CSS）去运行。页面或随后的页面之间的相互作用不需要请求服务器，这意味着页面没有被重新加载。

尽管你可以使用React 中构建一个单页应用程序，但这不是一个必须的要求。React 还可用于通过额外的交互来增强现有网站的小部分。用React 编写的代码可以与PHP 之类的服务器上，或其他客户端库标记渲染和平共存。事实上，这正是React 在Facebook 中的使用方式。

### ES6, ES2015, ES2016, etc

这些缩略词都指的是ECMAScript语言规范标准的最新版本，JavaScript 语言是它们中的一个实现。es6 版本（也被称为es2015）同以前的版本相比包括许多新增的，如：箭头的函数，类，模板字符串，`let` 和`const` 语句。你可以在[这里](https://en.wikipedia.org/wiki/ECMAScript#Versions)了解更多的特定版本。

### 编译器（Compliers）

JavaScript 编译器接收JavaScript 代码，转换JavaScript 代码并返回不同格式的JavaScript 代码。最常见的情况是把es6 语法转换成旧的浏览器都能解释的语法。[Babel](https://babeljs.io/) 是React 最常用的编译器。

### 打包器（Bundlers）

打包器将JavaScript 和CSS 代码作为不同的模块编写(通常是数百个模块)，并将它们合并到几个文件中，以便更好地为浏览器进行优化。一些在React 应用程序中常用的打包器包括[Webpack](https://webpack.js.org/) 和[Browserify](http://browserify.org/)。

### 包管理器（Package Managers）

包管理器是允许你管理项目中的依赖项的工具。[npm](https://www.npmjs.com/) 和[Yarn](http://yarnpkg.com/) 是React 应用中常用的包管理。他们都是同样的npm 包注册。

### 内容分发网络（CDN）

CND 是内容分发网络（Content Delivery Network）的缩略词。CDN 从全球服务器网络提供缓存和静态内容。

### JSX

JSX 是JavaScript 的语法扩展。它类似于模板语言，但它具有JavaScript 的全部功能。JSX 通过`React.createElement` 的调用进行编译。c返回普通的JavaScript的对象称为“React Element”。[这是](https://reactjs.org/docs/introducing-jsx.html)JSX 一个基本的介绍文档，而[这是](https://reactjs.org/docs/jsx-in-depth.html) JSX更深入的教程。

React DOM 使用CamelCase 属性命名约定来代替HTML属性名称。例如，`tabindex` 变成在JSX的`tabIndex`。属性`class` 被写为`className`， 因为`class` 是JavaScript中的保留字：

```
const name = 'Clementine';
ReactDOM.render(
  <h1 className="hello">My name is {name}!</h1>,
  document.getElementById('root')
);
```

### [元素（Elements）](https://reactjs.org/docs/rendering-elements.html)

元素是React 应用程序的组成部分。人们可能会混淆元素与更广为人知的“组件”概念。元素描述你想在屏幕上看到的内容。元素是不可变的。

```
const element = <h1>Hello, world</h1>;
```

通常，元素不直接使用，而是从组件中返回。


### [组件（Components）](https://reactjs.org/docs/components-and-props.html)

React 组件是细小的、可重用的代码块，返回一个将要渲染到页面React 元素。React 组件最简单的版本是返回React 元素的普通JavaScript函数：

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

组件也可以是ES6 类：

```
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

组件可以分解成不同的功能部件，并在其他组件中使用。组件可以返回其他组件、数组、字符串和数字。一个好的经验法则是，如果你的UI的一部分被多次使用（按钮、面板、头像），或是对他自己（APP，feedstory，评论）足够复杂，这是一个很好的可重用的组件的候选人。组件名称也应该总是以大写字母开头（`<Wraper/>`不`<wraper/>`）。有关渲染组件的更多信息，请参见[此文档](https://reactjs.org/docs/components-and-props.html#rendering-a-component)。


### [props](https://reactjs.org/docs/components-and-props.html)

`props` 是React 组件的输入。它们是从父组件传递到子组件的数据。
记住，`props` 是只读的。不应以任何方式加以修改：

```
// Wrong!
props.number = 42;
```

如果你需要修改一些值去对用户输入的或网络返回的值做出响应，请使用`state`。


### props.children

`props.children` 可用在每一个组件。它包含组件的打开和结束标记之间的内容。例如：

```
<Welcome>Hello world!</Welcome>
```

`Hello world!` 字符串可在`Welcome` 组件中使用`props.children`：

```
function Welcome(props) {
  return <p>{props.children}</p>
}
```

对于由类定义的组件，使用`this.props.children`：

```
class Welcome extends React.Component {
  render() {
    return <p>{this.props.children}</p>
  }
}
```

### [state](https://reactjs.org/docs/state-and-lifecycle.html#adding-local-state-to-a-class)

当与它相关的某些数据随时间变化时，组件需要`state` 。例如，`Checkbox`组件可能需要`isChecked` 的`state`，`NewsFeed` 组件可能想跟踪其`state` 的`fetchedPosts`。

`state` 和`props` 之间最重要的区别在于，`props` 是从父组件传递的，而`state` 是由组件本身管理的。组件不能改变它的`props` ，但它可以改变它的`state` 。这样做，它必须调用`this.setState()`。只有定义为类的组件才能具有`state`。

对于每个特定的变化数据，应该只有一个组件“拥有”它的`state`。不要试图同步两个不同组件的`state`。相反，[把它提升](https://reactjs.org/docs/lifting-state-up.html)到最接近的祖先，并把它作为`props` 传给他们两个。

### [生命周期方法（Lifecycle Methods）](https://reactjs.org/docs/state-and-lifecycle.html#adding-lifecycle-methods-to-a-class)

生命周期方法是在组件的不同阶段执行的自定义功能。当组件被创建并插入到DOM （[挂载mounting](https://reactjs.org/docs/react-component.html#mounting)）、组件更新以及组件被卸载或从DOM 中删除时，都有可用的方法。

### [受控的（Controlled）](https://reactjs.org/docs/forms.html#controlled-components) vs. [不可控的（Uncontrollled Components）](https://reactjs.org/docs/uncontrolled-components.html)

React 有两种不同的处理表单输入的方法。

输入表单元素的值由Redact 控制的称为受控组件。当用户将数据输入受控制的组件时，将触发一个更改事件处理器，你的代码将决定输入是否有效（通过更新的值重新渲染）。如果不重新渲染，表单元素将保持不变。

一个不受控制的组件像表单元素一样工作在React 之外。当用户将数据输入表单字段（输入框、下拉列表等）时，更新的信息被反射出来，而不需要React 做任何事情。然而，这也意味着不能强迫字段域（field）具有一个确定的值。

在大多数情况下，你应该使用受控组件。

### [Keys](https://reactjs.org/docs/lists-and-keys.html)

“key” 是创建元素数组时需要包含的一个特殊字符串属性。Keys 有助于识别哪些项目已经更改、添加或删除。应该给数组中的元素赋予key，使元素具有稳定的标识。

Key 只需要在同一数组中的同级元素之间是唯一的。它们不需要在整个应用程序中甚至单个组件中都是唯一的。

不传递像`Math.random()` 给key。重要的是，key 在重新渲染时有一个“稳定的标识”，所以React 可以决定何时添加、移除或重新排序。理想情况下，Key应符合独特的稳定来自于你的数据的标识符，如`post.id`。


### [Refs](https://reactjs.org/docs/refs-and-the-dom.html)

React 支持你可以附加到任何组件的特殊属性。`ref` 属性可以是字符串或回调函数。当`ref` 属性是一个回调函数时，函数接收底层DOM元素或类实例（取决于元素的类型）作为参数。这允许你直接访问DOM元素或组件实例。

谨慎使用`ref` 。如果你发现自己经常在应用程序中使用`ref` “让事情发生”，那么考虑一下更熟悉的[自上而下的数据流](https://reactjs.org/docs/lifting-state-up.html)。

### [事件（Events）](https://reactjs.org/docs/handling-events.html)

使用React 元素处理事件有一些语法差异：
* React 事件处理程序使用CamelCase 命名，而不是小写。
* 在JSX 中你传递一个函数作为事件处理程序，而不是一个字符串。

### [Reconciliation](https://reactjs.org/docs/reconciliation.html)

当组件的`props` 或`state` 发生变化时，通过将新返回的元素与先前渲染的元素进行比较，React 决定是否需要实际的DOM 更新。当它们不相等时，React 将更新DOM。这个过程叫做“reconciliation”。
