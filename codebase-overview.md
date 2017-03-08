> 此文章是翻译[Codebase Overview](https://facebook.github.io/react/contributing/codebase-overview.html)这篇React（版本v15.4.0）官方文档。

## Codebase Overview

本节将概要介绍React 代码库的组织，约定已经实现。

如果你想要[contribute to React](https://facebook.github.io/react/contributing/how-to-contribute.html)， 我们希望这个指南能够帮你更舒服地改变。

我们不一定推荐React 应用中的任何约定。它们中的多数是由于历史原因存在，可能会随着事件而改变。

### Custom Module System

在Facebook 内部我们使用自定义的模块系统“Haste”。它和[CommonJS](https://nodejs.org/docs/latest/api/modules.html) 非常类似，也是`require()`方法，但是有几个重要的差异，往往会混淆外部贡献者。

在CommonJS 中，当你引入一个模块，你可以配置它的相对路径
```jsx
// Importing from the same folder
var setInnerHTML = require('./setInnerHTML');

// Importing from a different folder
var setInnerHTML = require('../utils/setInnerHTML');

// Importing from a deeply nested folder
var setInnerHTML = require('../client/utils/setInnerHTML');
```
然而，使用Haste需要**所有的文件名都是全局唯一的**。 在React 代码库中，你可以单独的根据它的名字从任何其它模块导入任何模块：
```jsx
var setInnerHTML = require('setInnerHTML')
```
Haste 最初是为了像Facebook 这样的大型网站开发的。很容易将文件移动到不同的文件夹并导入它们，而不必担心相对路径。在任何编辑器中的模糊文件搜索总是能定位到正确的位置，由于全局唯一的名称。

React 本身是从Facebook 代码库中提取出来的，使用Haste 是由于历史原因。在未来，我们将可能[迁移React 到使用CommonJS 或ES 模块](https://github.com/facebook/react/issues/6336)来同其它社区对齐。然而，这需要在Facebook 内部的基础设施的改变，所以它不太可能很快发生。

#### Haste will make more sense to you if you remember a few rules:

* 所有的文件名在React 源代码中是唯一的。这就是为什么它们很冗余。
* 当你添加一个新的文件，确保你包括了一个[版权头（license header）](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/dom/client/utils/setInnerHTML.js#L1-L10)。你可以从其它已经存在的文件中复制。版权头总是包含[a line like this](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/dom/client/utils/setInnerHTML.js#L9)。修改它去匹配你创建的文件的名称。
* 当引入的时候，不要使用相对路径。使用`require('setInnerHTML')` 而不是`require('./setInnerHTML')`。

当我们编译React 为npm，一个脚本复制所有的模块到[一个单一的扁平目录`lib`]()，并且为所有`require()` 的路径中使用`./` 前置。这种方式Node，Browserify，Webpack 已经其它工具都能理解React 构建输出，而不会意识到Haste。

#### If you're reading React source on GitHub and want to jump to a file, press "t".

这是GitHub 快捷键，在当前仓库（repo）中模糊文件名搜索匹配。开始键入你要查找的文件的名称，它将作为第一次匹配显示。

### External Dependencies

React 几乎没有外部依赖。通常，一个`require()` 指出React 自己代码库中的一个文件。然而， 也有一些相对罕见的例外。

如果你看到一个`require()` 不能响应React 仓库中一个文件，你可以查看一个[fbjs](https://github.com/facebook/fbjs) 的特殊仓库。例如，`require('warning')` 将解析[`warning` module from fbjs](https://github.com/facebook/fbjs/blob/df9047fec0bbd1e64635ae369c045975777cba7c/packages/fbjs/src/__forks__/warning.js)。

[fbjs repository](https://github.com/facebook/fbjs) 存在是因为React 共享一些性[Relay]库的工具，我们保持它们同步。我们不依赖NOde 生态系统的相同的小模块，因为我们想要Facebook 工程师必要时可以修改它们。fbjs 中没有工具被认为是公共的API，它们只是在Facebook 项目中例如React 中使用。


### Top-Level Folders

克隆完[React repository] 之后，你将看到几个顶级文件夹：

* [src](https://github.com/facebook/react/tree/master/src) 是React 源代码。**如果你的改变是关于代码的，`src` 将花费你大量的时间。**
* [docs](https://github.com/facebook/react/tree/master/docs) 是React 的文档网站。当你修改了API，请确保更新相关的Markdown 文件。
* [examples](https://github.com/facebook/react/tree/master/examples) 包含了几个使用不同构建设置的React 小例子。
* [packages](https://github.com/facebook/react/tree/master/packages) 包含了React 仓库中所有代码的元数据（像`package.json`），尽管如此，它们的源代码仍然位于[src](https://github.com/facebook/react/tree/master/src)。
* `build` 是React 构建输出。它不再仓库中但是当你第一次[构建](https://facebook.github.io/react/contributing/how-to-contribute.html#development-workflow) 它，在你的React 副本中将会显示。

还有一些其它的顶级文件夹，但是它们通常用于工具，当你贡献代码时，通常不会遇到它们。

### Colocated Test

我们还没有一个顶级目录作为单元测试。相反，我们把它们放到相对测试的文件的`__tests__` 目录。

例如，一个[setInnerHTML.js](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/dom/client/utils/setInnerHTML.js) 测试位于[__tests__/setInnerHTML-test.js](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/dom/client/utils/__tests__/setInnerHTML-test.js) 的旁边。


### Shared Code

尽管Haste 允许我们导入位于仓库中任意位置的模块，但是我们遵循一个规则去避免循环依赖和其他不愉快的惊喜。按照惯例，一个文件只能岛屿同一个文件夹下或子文件夹下。

例如，位于[src/renderers/dom/stack/client](https://github.com/facebook/react/blob/f53854424b33692907234fe7a1f80b888fd80751/src/renderers/dom/stack/client) 的文件可以同一文件夹下或上一个文件夹下的其他文件。

然而，它们不能导致位于[src/renderers/dom/stack/server](https://github.com/facebook/react/blob/f53854424b33692907234fe7a1f80b888fd80751/src/renderers/dom/stack/server) 的模块，因为它不是[src/renderers/dom/stack/client](https://github.com/facebook/react/blob/f53854424b33692907234fe7a1f80b888fd80751/src/renderers/dom/stack/client) 的子目录。

这个规则有一个例外。有时我们需要在两组模块之间共享功能。在这种情况下，我们提升共享模块到`shared` 文件夹，此文件夹位于需要依赖它的最靠近的共同的祖先。

例如，在 [src/renderers/dom/stack/client](https://github.com/facebook/react/blob/f53854424b33692907234fe7a1f80b888fd80751/src/renderers/dom/stack/client) 和 [src/renderers/dom/stack/server](https://github.com/facebook/react/blob/f53854424b33692907234fe7a1f80b888fd80751/src/renderers/dom/stack/server)共享的代码位于[src/renderers/dom/shared](https://github.com/facebook/react/blob/f53854424b33692907234fe7a1f80b888fd80751/src/renderers/dom/shared)。

按照同样的逻辑，如果[src/renderers/dom/stack/client](https://github.com/facebook/react/blob/f53854424b33692907234fe7a1f80b888fd80751/src/renderers/dom/stack/client) 需要和[src/renderers/native](https://github.com/facebook/react/blob/f53854424b33692907234fe7a1f80b888fd80751/src/renderers/native) 共享一些工具，这些工具位于[src/renderers/shared](https://github.com/facebook/react/blob/f53854424b33692907234fe7a1f80b888fd80751/src/renderers/shared)。

这个规则不是强制的，但是我们在对一个推送请求复审使将会进行检测。

### Warnings and Invariants

React 代码库使用`waring` 模块去显示警告：
```jsx
var warning = require('warning');

warning(
  2 + 2 === 4,
  'Math is not working today.'
);
```

#### The warning is shown when the `warning` condition is `false`

考虑一种方式，条件应该响应正常环境而不是异常环境。

这是一个好主意去避免控制台中重复的警告的垃圾信息。

```jsx
var warning = require('warning');

var didWarnAboutMath = false;
if (!didWarnAboutMath) {
  warning(
    2 + 2 === 4,
    'Math is not working today.'
  );
  didWarnAboutMath = true;
}
```
警告只在开发环境下有效。在生产环境，它们完全被跳过。如果你需要禁止某些代码路径执行，请使用`invariant` 模块：
```jsx
var invariant = require('invariant');

invariant(
  2 + 2 === 4,
  'You shall not pass!'
);
```

#### The invariant is thrown when the `invariant` condition is `false`

“不变（invariant）”只是“这个条件总保持为真”的一种说法。你可以认为它是一个明确肯定（assertion）。

保持在开发和生产行为相似是重要的，因为`invariant` 在开发和生产都抛出。错误信息在生产中自动替换为错误代码，以避免字节大小的负面影响。

### Development and Production

你可以在代码库中使用`__DEV__` 伪全局变量来包括只在开发环境中的代码块。

在编译步骤中它是内联的（inlined），在CommonJS 构建中，它转成`process.env.NODE_ENV !== 'production'`去检测。

对于独立的构建，它在unminified 的构建中变成`true`，完全跳过在minified 构建中它保护的`if` 块。
```jsx
if(__DEV__){
     // This code will only run in development.
}
```

### JSDoc

一些颞部公共方法使用[JSDoc annotations](http://usejsdoc.org/) 注释：
```jsx
/**
  * Updates this component by updating the text content.
  *
  * @param {ReactText} nextText The next text content
  * @param {ReactReconcileTransaction} transaction
  * @internal
  */
receiveComponent: function(nextText, transaction) {
  // ...
},
```
我们试图保持现有注释的更新，但是我们并不强制。我们在新编写代码中不使用JSDoc，而是使用Flow 去写文档和强制类型。

### Flow

我们最近开始引入[Flow](https://flowtype.org/) 检测代码库。使用`@flow` 注释在许可头部的注解的文件正在被检测。

我们接受推送请求[adding Flow annotations to existing code](https://github.com/facebook/react/pull/7600/files)。Flow 注解看上去像这样：
```jsx
ReactRef.detachRefs = function(
  instance: ReactInstance,
  element: ReactElement | string | number | null | false,
): void {
  // ...
}
```
尽可能的，新的代码应该使用Flow 注解。

你可以在本地使用`npm run flow` 去检测你的代码。

### Classes and Mixins

React 使用ES5 原生写的。我们已经能够通过[Babel]() 支持ES6 特性，包括类（classes）。然而，大多数React代码仍然是使用ES5 写的。

特别是，你可能会经常看到以下模式：
```jsx
// Constructor
function ReactDOMComponent(element){
  this._currentElement = element;
}

// Methods
ReactDOMComponent.Mixin = {
  mountComponent: function(){
    // ...
  }
};

// Put methods on the prototype
Object.assign(
  ReactDOMComponent.prototype,
  ReactDOMComponent.Mixin
);

module.exports = ReactDOMComponent;
```
代码中的`Mixin` 同React 中的`mixins` 特性没有关系。它只是一种将几个方法分组到一个对象下 。这些方法可能会被绑定到其他的类（class）上。我们在许多地方使用这种模式，虽然我们尝试在新代码中避免这种方式。

相当的ES6  代码可能会像这样：
```jsx
class ReactDOMComponent{
  constructor(element){
    this._currentElement = element;
  }

  mountComponent(){
    // ...
  }
}


module.exports = ReactDOMComponent;
```

有时我们会[convert old code to ES6 classes](https://github.com/facebook/react/pull/7647/files)。然而，对我们来说这并不重要，因为这里有一个替代React reconcilar 实现通过更少的面向对象的（object-oriented）方式将不会使用类而正在[进行的努力（ongoing effort）](https://facebook.github.io/react/contributing/codebase-overview.html#fiber-reconciler)。

### Dynamic Injection

在一些模块中React 使用动态注入。虽然它总是明确的，但它仍然是不幸的，因为它阻碍了对代码的理解。它存在的主要原因是React 最初只支持DOM 作为目标。React Native 开始是作为React 的分支。我们必须加入动态注入来让React Native 覆盖一些行为。

你可以看到模块声明它们的动态依赖关系：
```jsx
// Dynamically injected
var textComponentClass = null;

// Relies on dynamically injected value
function createInstanceForText(text){
  return new textComponentClass(text);
}

var ReactHostComponent = {
  createInstanceForText,

  // Provides an opportunity for dynamic injection
  injection: {
    injectTextComponentClass: function(componentClass){
      textComponentClass = comonentClass;
    },
  },
};

module.exports = ReactHostComponent;
```
`injection` 域不是被任何特定的方式处理。但是按照管理，这意味着这个模块系统啊在运行时有一些（大概是平台特定的（platform-specific））依赖注入。

在React DOM 中，[ReactDefaultInjection](https://github.com/facebook/react/blob/4f345e021a6bd9105f09f3aee6d8762eaa9db3ec/src/renderers/dom/shared/ReactDefaultInjection.js) 注入一个DOM 特定的（DOM-specific）实现：
```jsx
ReactHostComponent.injection.injectTextComponentClass(ReactDOMTextComponent);
```

在React Native 中，[ReactNativeDefaultInjection](https://github.com/facebook/react/blob/4f345e021a6bd9105f09f3aee6d8762eaa9db3ec/src/renderers/native/ReactNativeDefaultInjection.js)注入一个它自己的实现：
```jsx
ReactHostComponent.injection.injectTextComponentClass(ReactNativeTextComponent);
```
在代码库中有多个注入点。在未来，我们打算摆脱动态注入机制并且在构建时静态地绑定所有的块。


### Multiple Packages

React 是一个[monorepo](http://danluu.com/monorepo/)。它的仓库包括多个独立的包，使得它们的改变可以协调在一起，并且文档和问题都位于一个位置。

npm 元数据就像`package.json` 文件位于[packages]() 顶级文件夹。然而，几乎没有真正的代码在这里。

例如，[packages/react/react.js](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/packages/react/react.js) 重新导出到[src/isomorphic/React.js](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/isomorphic/React.js)，真正的npm 入口点。其它的包主要是重复这个模式。所有的重要的代码都位于[src](https://github.com/facebook/react/tree/master/src)。

当代码重源代码树中分离出来，npm 包和独立的浏览器构建提取出来的包的边界是稍有不同的。

### React Core

React 的“核心（core）”包括所有的[顶级`React`API](https://github.com/facebook/react/tree/master/src)，例如：

* `React.createElement()`
* `React.createClass()`
* `React.Component`
* `React.Children`
* `React.PropTypes`

**React core only includes the APIs necessary to define component**。它不需要包含[reconciliation](https://facebook.github.io/react/docs/reconciliation.html)索然或任何平台特定的（platform-specific）代码。它被用于React DOM 和React Native components。

React core 代码位于源代码树的[src/isomorphic](https://github.com/facebook/react/tree/master/src/isomorphic)。它是npm 作为[react](https://www.npmjs.com/package/react) 包是合适的。相应的独立的浏览器构建被称为`react.js`并且它导出一个全局`React`。

>**Note：**
直到最近，`react` npm 包和`react.js` 独立构建包含所有的React 代码（包括React DOM）而是仅仅这个核心。这么做是为了向后兼容和历史原因。直到React 15.4.0，和兴是更好的分离在构建输出。
也有一些额外的独立的浏览器构建称为`react-with-addons.js`我们将在未来考虑分离它。

### Renderers

React 最初是为了DOM 创建的但是最后它也改变为通过[React Native]() 支持本地平台。接下来介绍React 内部的“渲染（renderers）”概念。

**Renderers manange how a React tree turns into the underlying platform calls.**

Renderers 位于[src/renderers](https://github.com/facebook/react/tree/master/src/renderers/)：

* [React DOM Renderer](https://github.com/facebook/react/tree/master/src/renderers/dom) 渲染React component 为DOM。它实现了[top-level `ReactDOM` APIs](https://facebook.github.io/react/docs/top-level-api.html#reactdom) 并且作为[react-dom](https://www.npmjs.com/package/react-dom) npm 包是有效的。它也可以被用作独立的浏览器插件`react-dom.js`并且导出一个全局`ReactDOM`。
* [React Native Renderer](https://github.com/facebook/react/tree/master/src/renderers/native) 渲染React component 为本地视图（native view）。它被用作React Native 内部通过[react-native-renderer](https://www.npmjs.com/package/react-native-renderer) npm 包。在未来它的一份复制可能被检入（get checked into）到React Native [代码库](https://github.com/facebook/react-native)，导致React Native 可以在它自己的空间更新React。
* [React Test Renderer](https://github.com/facebook/react/tree/master/src/renderers/testing) 渲染React component 为JSON 树。它被[Jest](https://facebook.github.io/jest) 的[Snapshot Testing](https://facebook.github.io/jest/blog/2016/07/27/jest-14.html) 特性所用，并且作为[react-test-renderer](https://www.npmjs.com/package/react-test-renderer) npm 包使用。

这唯一的其他官方支持的渲染器是[react-art](https://github.com/reactjs/react-art)。为了避免我们在改变React 时破环它，我们将它作为[src/renderers/art](https://github.com/facebook/react/tree/master/src/renderers/art) 检入（check in）并运行它的测试套件。尽管如此，它的[GitHub repository](https://github.com/reactjs/react-art) 仍然作为真理源（the source of truth）。

虽然[技术上可以](https://github.com/iamdustan/tiny-react-renderer)创建自定义的React renderer，这时没有官方支持。对于自定义渲染器目前没有稳定的公共协议，这也是另一个我们保存同在单独一个地方的原因。

>**Note：**
技术上[native](https://github.com/facebook/react/tree/master/src/renderers/native)渲染器是一个非常薄的层，教导React 同React 实现交互。真正的平台特定的（platform-specific）代码管理本地视图位于[React Native repository](https://github.com/facebook/react-native)和它的component 在一起。

### Reconcilers

即使像React DOM 和React Native 在渲染器上非常不同，仍然共享许多逻辑。尤其是，[reconciliation]() 算法应该尽可能的相似，以至于像声明式渲染，自定义component，state，lifecycel methods，和refs 仍然是跨平台一致。

为了解决它，不同的渲染器在它们之间共享一些代码。我们成React 的这部份为“reconciler”。当一个更新例如`setState` 被调度，这个reconciler 调用书中的component 上的`render()` 去加载、更新、或卸载它们。

Reconcilear 没有独立为包，因为它们目前还没有公共的API。相反，它们专门被用作渲染器例如React DOM 和React Native

### Stack Reconciler

目前“stack”reconciler 是所有React 生产代码中最有效的。它位于[src/renderers/shared/stack/reconciler](https://github.com/facebook/react/tree/master/src/renderers/shared/stack) 并且被React DOM 和React Native 使用。

它以[面向对象的方式]()书写，并为所有的React component 维护一个单独的“内部实例（internal instance）”树。内部实例存在于用户定义的（“composite”）和平台特定的（“host”）component。用户无法直接访问内部实例，并且它们的树从不暴露。

当component 加载、更新或者是卸载时，stack reconciler 调用内部实例上的方法。这些方法是`mountComponent(element)`、`recevieComponent(nextElement)`和`unmountComponent(element)`。

#### Host Components

平台特定的（“host”）component，就像`<div>`和`<View>`，运行平台特定的代码。例如，React DOM 指示stack reconciler 使用[`ReactDOMComponent`](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/dom/shared/ReactDOMComponent.js) 去控制DOM component 的[mounting](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/dom/shared/ReactDOMComponent.js#L517)、[update](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/dom/shared/ReactDOMComponent.js#L865) 和[unmounting](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/dom/shared/ReactDOMComponent.js#L1140)。

无关平台，`<div>` 和`<View>` 以相似的方式处理管理多个子节点。为方便起见，stack reconciler 提供一个助手[`ReactMultiChild`](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/shared/stack/reconciler/ReactMultiChild.js) 为DOM 和Native 渲染去[使用](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/dom/shared/ReactDOMComponent.js#L1203)。

#### Composite Components

用户定义的（“composite”）components 应该和所有的渲染器有相同的行为。这就是为什么stack reconciler 在[`ReactCompositeComponent`](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/shared/stack/reconciler/ReactCompositeComponent.js) 中提供一个共享的实现。它总是相同的无关渲染器。

Composite components 也实现了[mounting](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/shared/stack/reconciler/ReactCompositeComponent.js#L181)、[updating](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/shared/stack/reconciler/ReactCompositeComponent.js#L703) 和[unmounting](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/shared/stack/reconciler/ReactCompositeComponent.js#L524)。然而，不像host components，`ReactCompositeComponent` 需要基于用户代码而有不同的行为。这就是为什么它调用的方法，像`render()` 和`componentDidMount()` 在用户提供的（user-supplide） 类上。

在一次更新阶段，`ReactCompositeComponent` 检查`render()` 输出是否有一个通过的`type` 或者`key` 同上一次更新。如果`type` 和`key` 都没有更改，它将代理已存在的孩子内部实例的更新。然而，它卸载旧的子节点实例并且加载一个新的子节点实例。这个在[reconciliation algorithm](https://facebook.github.io/react/docs/reconciliation.html)有描述。

#### Recursion

在更新过程中，stack reconciler “向下获取数据（drill down ）”通过composite components，运行它们的`render()` 方法，描述是否去更新或替代它们单个子实例。它执行平台特定的代码当它通过host components 像`<div>` 和`<View>`。Host components 可以有多个子节点，也可以通过递归（recursively）处理。

这是重要的去了解stack reconciler 总是在一次过程中同步处理component 树。虽然单独的树可能[bail out of reconciliation](https://facebook.github.io/react/docs/advanced-performance.html#avoiding-reconciling-the-dom)，stack reconciler 不能暂停，并且当更新是深的，可用的CPU 事件是有限的时，它不是最理想的。

#### Learn More

**[下一章节](https://facebook.github.io/react/contributing/implementation-notes.html)更新的描述当前实现**

### Fiber Reconciler

“fiber”reconciler 是一个新的努力去解决stack reconciler 中固有的问题和一些长期存在的问题。

它完全重写了这个reconciler，当前处于[in active development]()。

它的主要目标是：

* 能够分块可中断工作的能力
* 能够优先、复位和重用工作进程中的能力
* 在父节点和子节点之间来回支持布局在React 中的能力
* 从`render()` 中返回多个element 的能力
* 更好支持错误边界

你可以在[React Fiber Architecture](https://github.com/acdlite/react-fiber-architecture) 中了解更多。目前，它仍然是试验性的，距离和stack reconciler 特性等价是遥远的。

它的源代码位于[src/renderers/shared/fiber](https://github.com/facebook/react/tree/master/src/renderers/shared/fiber)

### Event System

React 实现了一个合成的事件系统，它是渲染器不可知的，在React DOM 和React Native 中工作。它的源代码位于[src/renderers/shared/shared/event](https://github.com/facebook/react/tree/master/src/renderers/shared/shared/event)。

这有一个[video with a deep code dive into it](https://www.youtube.com/watch?v=dRo_egw7tBc) 60 分钟。


### Add-ons

每一个[React add-ons](https://facebook.github.io/react/docs/addons.html) 传送作为一个单独npm 包通过`react-addons-` 前缀。它们的源代码位于[src/addons](https://github.com/facebook/react/tree/master/src/addons)，[ReactPerf](https://github.com/facebook/react/blob/master/src/renderers/shared/ReactPerf.js) 和[ReactTestUtils](https://github.com/facebook/react/blob/master/src/test/ReactTestUtils.js) 是例外。

此外，我们提供了一个独立的构建`react-with-addons.js`，它包括React core 和所有的add-ons 暴露在`React` 这个全局对象的`addons` 域上。

### What Next？

阅读[next section](https://facebook.github.io/react/contributing/implementation-notes.html) 去了解关于当前reconciler 实现的更多细节。
