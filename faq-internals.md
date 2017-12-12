> 此文章是翻译[Virtual DOM and Internals](https://reactjs.org/docs/faq-internals.html)这篇React（版本v16.2.0）官方文档。

## Virtual DOM and Internals

### 什么是虚拟DOM（Virtual DOM）？

虚拟DOM（VDOM）是一种编程概念，是一种理想的、或“虚拟的” 表示在内存中的UI 视图通过类似于ReactDOM 这种库同“真实的”DOM 进行同步。这一过程称为[reconciliation](https://reactjs.org/docs/reconciliation.html)。

这种方式使用了React 的声明式API：你告诉React你想让UI 视图处于什么状态，React则负责确保DOM与该状态相匹配。抽出了属性操作、事件处理、DOM更新等本来需要你使用这些去构建应用。

由于“虚拟DOM”是一种模式，而不是一种特定的技术，人们有时说它意味着不同的东西。在React 中，“虚拟DOM”一词通常与React 元素相关联，因为它们都是表示用户界面的对象。然而，React 也使用称为“fiber”的内部对象来保存组件树的附加信息。它们也可以被看作是React 中“虚拟DOM”实现的一部分。

### 阴影DOM（Shadow DOM）同虚拟DOM（Virtual DOM）一样吗？

它们是不同的。阴影DOM 是一项浏览器技术，被用来在web 组件中保存变量和CSS。虚拟DOM 是使用浏览器顶级API JavaScript 库实现的一种概念。

### 什么是“React Fiber”？

Fiber 是React 16 中新的reconciliation 引擎。它的主要目标是增强虚拟DOM 的渲染能力。[了解更多](https://github.com/acdlite/react-fiber-architecture)。
