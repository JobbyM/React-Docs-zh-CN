> 此文章是翻译[Component State](https://reactjs.org/docs/faq-state.html)这篇React（版本v16.2.0）官方文档。

## Component State

### setState 做了什么？

`setState()` 安排更新一个组件的`state` 对象。当state 改变时，将重新渲染组件。

### state 和props 的区别是什么？

[props](https://reactjs.org/docs/components-and-props.html)（是“properites” 的简写）和[state](https://reactjs.org/docs/state-and-lifecycle.html) 都只是JavaScript 对象，发生改变时会触发重新渲染。同时都保存会影响渲染输出的信息，它们在一个非常重要的方面是不同的：`props` 被传递给组件（类似与函数参数），然而`state` 是在组件内被管理的（类似于函数中的变量声明）。

这有一些好资源去进一步阅读什么时候去使用`props` 或`state` ：

* [Props vs State](https://github.com/uberVU/react-guide/blob/master/props-vs-state.md)
* [ReactJS: Props vs. State](http://lucybain.com/blog/2016/react-state-vs-pros/)


### 为什么setState 给我这错误的值？

`setState`的调用是异步的--不要在调用`setState` 之后立即依赖`this.state` 去影响新的值。传入一个更新函数（updater function），而不是一个对象，如果你需要根据当前状态计算值（详情见下文）。

不符合预期的代码示例：

```
incrementCount() {
  // Note: this will *not* work as intended
  this.setState({count: this.state.count + 1});
}

handleSomething() {
  // this.state.count is 1, then we do this:
  this.incrementCount();
  this.incrementCount(); // state wasn't updated yet, so this sets 2 not 3
}
```

有关如何修复这个问题，请参见下文。

### 我如何根据当前state 的value 去更新state？

传入一个函数而不是对象在setState 中却确保这个调用总是使用最新的state 版本（看下面）。

### 在setState 中传入对象和函数的区别是什么？

传入一个更新函数允许你在更新函数内访问当前state 的值。由于`setState` 的调用是打包处理（batched），使得你链式更新来确保它们建立在彼此的顶端而不冲突。

```
incrementCount() {
  this.setState((preState) => {
    return {count: preState.count + 1}
  });
}

handleSomething() {
  // this.state.count is 1, then we do this:
  this.incrementCount();
  this.incrementCount(); // count is now 3
}
```

[了解setState 更多](https://reactjs.org/docs/react-component.html#setstate)

### 我应该使用像Redux 或MobX 的state 管理库吗？

[可能吧](http://redux.js.org/docs/faq/General.html#general-when-to-use)

在添加额外的库之前先了解React 是个好主意。你可以只使用React 构建相当复杂的应用程序。
