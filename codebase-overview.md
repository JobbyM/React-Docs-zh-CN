> 此文章是翻译[Codebase Overview](https://reactjs.org/docs/contributing/codebase-overview.html)这篇React（版本v16.2.0）官方文档。

## Codebase Overview

本节将概要介绍React 代码库的组织，它的约定，和实现。

如果你想要[contribute to React](https://reactjs.org/docs/contributing/how-to-contribute.html)， 我们希望这个指南能够帮你更舒服地进行改变。

我们不一定推荐React 应用中的任何约定。它们中的多数是由于历史原因存在，可能会随着时间而改变。

### External Dependencies

React 几乎没有外部依赖。通常，一个`require()` 指出React 自己代码库中的一个文件。然而， 也有一些相对罕见的例外。

[fbjs repository](https://github.com/facebook/fbjs) 存在是因为React 共享一些像[Relay](https://github.com/facebook/relay)库的工具，我们保持它们同步。我们不依赖NOde 生态系统的相同的小模块，因为我们想要Facebook 工程师必要时可以修改它们。fbjs 中没有工具被认为是公共的API，它们只是在Facebook 项目中例如React 中使用。

### Top-Level Folders

克隆完[React repository](https://github.com/facebook/react) 之后，你将看到几个顶级文件夹：

* [`packages`](https://github.com/facebook/react/tree/master/packages) 包含了React 仓库中所有代码的元数据（像`package.json`）和源代码（[`src`](https://github.com/facebook/react/tree/master/src) 子目录）为所有包。**如果你的改变是关于代码的，每一个包中的`src` 子目录将花费你大量的时间。**
* [`fixtures`](https://github.com/facebook/react/tree/master/fixtures) 包含了为贡献者提供的几个小的React 测试应用。
* `build` 是React 构建输出。它不在仓库中但是当你第一次[构建](https://reactjs.org/docs/contributing/how-to-contribute.html#development-workflow) 它，在你的React 副本中将会显示。

文档位于[React 不同的仓库中](https://github.com/reactjs/reactjs.org)。

还有一些其它的顶级文件夹，但是它们通常用于工具，当你贡献代码时，通常不会遇到它们。

### Colocated Test

我们还没有一个顶级目录作为单元测试。相反，我们把它们放到相对测试的文件的`__tests__` 目录。

例如，一个[setInnerHTML.js](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/dom/client/utils/setInnerHTML.js) 测试位于[__tests__/setInnerHTML-test.js](https://github.com/facebook/react/blob/87724bd87506325fcaf2648c70fc1f43411a87be/src/renderers/dom/client/utils/__tests__/setInnerHTML-test.js) 的旁边。

### Warnings and Invariants

React 代码库使用`waring` 模块去显示警告：

```
var warning = require('warning');

warning(
  2 + 2 === 4,
  'Math is not working today.'
);
```

#### The warning is shown when the `warning` condition is `false`

考虑一种方式，条件应该响应正常环境而不是异常环境。

这是一个好主意去避免控制台中重复的警告的垃圾信息。

```
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

```
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

你可以在代码库中使用`__DEV__` 伪全局变量来守护只在开发环境中的代码块。

在编译步骤中它是内联的（inlined），在CommonJS 构建中，它转成`process.env.NODE_ENV !== 'production'`去检测。

对于独立的构建，它在unminified 的构建中变成`true`，完全跳过在minified 构建中它保护的`if` 块。

```
if(__DEV__){
   // This code will only run in development.
}
```

### Flow

我们最近开始引入[Flow](https://flowtype.org/) 检测代码库。使用`@flow` 注释在许可头部的注解的文件正在被类型检测。

我们接受pull requests[adding Flow annotations to existing code](https://github.com/facebook/react/pull/7600/files)。Flow 注解看上去像这样：

```
ReactRef.detachRefs = function(
  instance: ReactInstance,
  element: ReactElement | string | number | null | false,
): void {
  // ...
}
```

尽可能的，新的代码应该使用Flow 注解。你可以在本地运行`yarn flow` 使用Flow 去检测你的代码。

### Dynamic Injection

在一些模块中React 使用动态注入。虽然它总是明确的，但它仍然是不幸的，因为它阻碍了对代码的理解。它存在的主要原因是React 最初只支持DOM 作为目标。React Native 开始是作为React 的分支。我们必须加入动态注入来让React Native 覆盖一些行为。

你可以看到模块声明它们的动态依赖关系，像这样：

```
// Dynamically injected
var textComponentClass = null;

// Relies on dynamically injected value
function createInstanceForText(text) {
  return new textComponentClass(text);
}

var ReactHostComponent = {
  createInstanceForText,

  // Provides an opportunity for dynamic injection
  injection: {
    injectTextComponentClass: function(componentClass) {
      textComponentClass = comonentClass;
    },
  },
};

module.exports = ReactHostComponent;
```

`injection` 域不是被任何特定的方式处理。但是按照约定，这意味着这个模块系统在运行时想要有一些（大概是平台特定的（platform-specific））依赖注入。

### Multiple Packages

React 是一个[monorepo](http://danluu.com/monorepo/)。它的仓库包括多个独立的包，使得它们的改变可以协调在一起，并且问题都位于一个位置。

### React Core

React 的“核心（core）”包括所有的[顶级`React`API](https://reactjs.org/docs/top-level-api.html#react)，例如：

* `React.createElement()`
* `React.Component`
* `React.Children`

**React core only includes the APIs necessary to define component**。它不需要包含[reconciliation](https://reactjs.org/docs/reconciliation.html)算法或任何平台特定的（platform-specific）代码。它被React DOM 和React Native components 使用。

React core 代码位于源代码树的[packages/react](https://github.com/facebook/react/tree/master/packages/react)。它是npm 作为[react](https://www.npmjs.com/package/react) 包是合适的。相应的独立的浏览器构建被称为`react.js`并且它导出一个全局`React`。

### Renderers

React 最初是为了DOM 创建的,但是最后它也改变为通过[React Native](http://facebook.github.io/react-native/) 支持本地平台。接下来介绍React 内部的“渲染器（renderers）”概念。

**Renderers manange how a React tree turns into the underlying platform calls.**

Renderers 位于[packages/](https://github.com/facebook/react/tree/master/packages/)：

* [React DOM Renderer](https://github.com/facebook/react/tree/master/packages/react-dom) 渲染React 组件为DOM。它实现了[top-level `ReactDOM` APIs](https://reactjs.org/docs/react-dom.html) 并且作为[react-dom](https://www.npmjs.com/package/react-dom) npm 包是有效的。它也可以被用作独立的浏览器插件`react-dom.js`并且导出一个全局`ReactDOM`。
* [React Native Renderer](https://github.com/facebook/react/tree/master/packages/react-native-renderer) 渲染React 组件为本地视图（native view）。它被React Native 内部使用。
* [React Test Renderer](https://github.com/facebook/react/tree/master/packages/react-test-renderer) 渲染React 组件为JSON 树。它被[Jest](https://facebook.github.io/jest) 的[Snapshot Testing](https://facebook.github.io/jest/blog/2016/07/27/jest-14.html) 特性所用，并且作为[react-test-renderer](https://www.npmjs.com/package/react-test-renderer) npm 包使用。

这唯一的其他官方支持的渲染器是[react-art](https://github.com/facebook/react/tree/master/packages/react-art)。它过去是在在独立的[GitHub repository](https://github.com/reactjs/react-art) 中使用，但是现在我们把它移动到主源树中。

>**注意：**

>技术上[react-native-renderer](https://github.com/facebook/react/tree/master/packages/react-native-renderer)是一个非常薄的层，教导React 同React Native实现交互。真正的平台特定的（platform-specific）代码管理本地视图和它的组件在一起位于[React Native repository](https://github.com/facebook/react-native)。

### Reconcilers

即使像React DOM 和React Native 在渲染器上非常不同，仍然共享许多逻辑。尤其是，[reconciliation](https://reactjs.org/docs/reconciliation.html) 算法应该尽可能的相似，以至于像声明式渲染，自定义组件，state，生命周期方法和refs 仍然是跨平台一致。

为了解决它，不同的渲染器在它们之间共享一些代码。我们称React 的这部份为“reconciler”。当一个更新例如`setState` 被调度，这个reconciler 调用树中的组件上的`render()` 去加载、更新、或卸载它们。

Reconcilear 没有独立为包，因为它们目前还没有公共的API。相反，它们专门被渲染器例如React DOM 和React Native 所使用。

### Stack Reconciler

“stack” reconciler 是实现由React 15和更早的版本。我们此后不再使用它，但在[下一节](https://reactjs.org/docs/implementation-notes.html)中详细记录了它。

### Fiber Reconciler

“fiber”reconciler 是一个新的努力去解决stack reconciler 中固有的问题和一些长期存在的问题。它自从React 16 已经是默认的reconciler。

它的主要目标是：

* 能够分块可中断工作的能力。
* 能够优先、复位和重用工作进程中的能力。
* 在父节点和子节点之间来回支持布局在React 中的能力。
* 从`render()` 中返回多个元素的能力。
* 更好支持错误边界。

你可以在[React Fiber Architecture](https://github.com/acdlite/react-fiber-architecture) 中了解更多。虽然它已经位于React 16 中，异步特性仍还没有被默认启用。

它的源代码位于[packages/react-reconciler](https://github.com/facebook/react/tree/master/packages/react-reconciler)

### Event System

React 实现了一个合成的事件系统，它是渲染器不可知的，在React DOM 和React Native 中工作。它的源代码位于[packages/events](https://github.com/facebook/react/tree/master/packages/events)。

这有一个[video with a deep code dive into it](https://www.youtube.com/watch?v=dRo_egw7tBc) 66 分钟。

### What Next？

阅读[下一章节](https://reactjs.org/docs/implementation-notes.html) 去了解关于之前reconciler 实现的更多细节。我们还没有记录新reconciler 的内部。
