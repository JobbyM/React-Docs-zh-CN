> 此文章是翻译[Test Utilities](https://facebook.github.io/react/docs/test-utils.html)这篇React（版本v15.4.0）官方文档。

## Test Utilities

**Importing**

```jsx
import ReactTestUtils from 'react-addons-test-utils' // ES6
var ReactTestUtils = require('react-addons-test-utils') // ES5 with npm
var ReactTestUtils = React.addons.TestUtils // ES5 with react-with-addons.js
```

## Overview

`ReactTestUtils` 便于在你选择的测试框架中测试React components。在Facebook，我们使用[Jest](https://facebook.github.io/jest/) 进行轻松的JavaScript测试。同Jest 网站的[React 使用说明](http://facebook.github.io/jest/docs/tutorial-react.html#content) 进行Jest 入门学习。


>**Note：**
Airbnb 已经发布了一个Enzyme 的测试工具，它可以容易地断言（assert）、操作（manipulate）、穿过（traverse）你的React component 的输出。如果你决定在单元测试工具和Jest 一起使用，或者其它的测试运行，非常值得查看：http://airbnb.io/enzyme/


* [Simulate](https://facebook.github.io/react/docs/test-utils.html#simulate)
* [renderIntoDocument()](https://facebook.github.io/react/docs/test-utils.html#renderintodocument)
* [mockComponent()](https://facebook.github.io/react/docs/test-utils.html#mockcomponent)
* [isElement()](https://facebook.github.io/react/docs/test-utils.html#iselement)
* [isElementOfType()](https://facebook.github.io/react/docs/test-utils.html#iselementoftype)
* [isDOMComponent()](https://facebook.github.io/react/docs/test-utils.html#isdomcomponent)
* [isCompositeComponent()](https://facebook.github.io/react/docs/test-utils.html#iscompositecomponent)
* [isCompositeComponentWithType()](https://facebook.github.io/react/docs/test-utils.html#iscompositecomponentwithtype)
* [findAllInRenderedTree()](https://facebook.github.io/react/docs/test-utils.html#findallinrenderedtree)
* [scryRenderedDOMComponentsWithClass()](https://facebook.github.io/react/docs/test-utils.html#scryrendereddomcomponentswithclass)
* [findRenderedDOMComponentWithClass()](https://facebook.github.io/react/docs/test-utils.html#findrendereddomcomponentwithclass)
* [scryRenderedDOMComponentsWithTag()](https://facebook.github.io/react/docs/test-utils.html#scryrendereddomcomponentswithtag)
* [findRenderedDOMComponentWithTag()](https://facebook.github.io/react/docs/test-utils.html#findrendereddomcomponentwithtag)
* [scryRenderedComponentsWithType()](https://facebook.github.io/react/docs/test-utils.html#scryrenderedcomponentswithtype)
* [findRenderedComponentWithType()](https://facebook.github.io/react/docs/test-utils.html#findrenderedcomponentwithtype)


## Shallow Rendering

浅渲染让你渲染一个“一级深度（one level deep）”的component，断言（assert） 事实是关于它的渲染方法的返回，不需要担心子component 的行为，它不是实例化或渲染。这不需要一个DOM。

* [createRenderer()](https://facebook.github.io/react/docs/test-utils.html#createrenderer)
* [shallowRenderer.render()](https://facebook.github.io/react/docs/test-utils.html#shallowrenderer.render)
* [shallowRenderer.getRenderOutput()](https://facebook.github.io/react/docs/test-utils.html#shallowrenderer.getrenderoutput)


在你的测试中调用[createRenderer()](https://facebook.github.io/react/docs/test-utils.html#createrenderer) 是为了创建一个浅渲染器。你可以认为这是一个“地方（place）”，以渲染你正在测试的component，并从中提取出component 的输出。

[shallowRenderer.render()](https://facebook.github.io/react/docs/test-utils.html#shallowrenderer.render) 和[ReactDOM.render()](https://facebook.github.io/react/docs/react-dom.html#render) 非常相似，但是它不需要DOM 并且只渲染一个一级深度（a single level deep）。这意味着你可以测试components，并且独立于它们子节点的实现。

当`shallowRenderer.render()` 已经被调用之后，你可以使用[shallowRenderer.getRenderOutput()](https://facebook.github.io/react/docs/test-utils.html#shallowrenderer.getrenderoutput) 去获取浅渲染的输出。

然后你可以断言关于（assert facts about）输出。例如，你有下面这个component：
```jsx
function MyComponent(){
  return (
    <div>
      <span className="heading">Title</span>
      <Subcomponent foo="bar" />
    </div>
  )
}
```
然后，你可以断言：
```jsx
const renderer = ReactTestUtils.createRenderer();
renderer.render(<MyComponent />);
const result = renderer.getRenderOutput();

expect(result.type).toBe('div')
expect(result.props.children).toEqual([
  <span clasName="heading">Title</span>,
  <Subcomponent foo="bar" />
])
```
浅测试当前还有一些限制，就是说还不支持refs。

我们建议查看Enzyme 的[Shallow Rendering API](http://airbnb.io/enzyme/docs/api/shallow.html)。它提供了一个更好更高级的API 来实现相同功能。

## Reference

### Simulate

```jsx
Simulate.{eventName}(
  element,
  [eventData]
)
```
使用可选的`eventData` 事件数据在一个DOM 节点上模拟一个事件分发。

`Simulate` 有一个[每一个React 理解](https://facebook.github.io/react/docs/events.html#supported-events)的方法

**Clicking an element**

```jsx
// <button ref="button">...</button>
const node = this.refs.button;
ReactTestUtils.Simulate.click(node);
```

**Changing the value of an input field and then pressing ENETER**

```jsx
// <input ref="input" />
const node = this.refs.input;
node.value = 'giraffe';
ReactTestUtils.Simulate.change(node);
ReactTestUtils.Simulate.keyDown(node, {key: "Enter", keyCode: 13, which: 13});
```

> **Note：**
你必须在你的component 中提供你正在使用的任何事件的属性（例如，keyCode，which 等等）因为React 不会为你创建任何东西。

### renderIntoDocument()

```jsx
renderIntoDocument(element)
```
渲染一个React element 到一个在文档中派遣的（detached）DOM 节点上。**这个方法需要一个DOM。**

>**Note：**
在你引入`React` **之前**，你必须有全局可用的`window`，`window.document`和`window.document.createElement`。否则React 将认为它不能访问DOM 并且像`setState` 的方法将不会工作。

### mockComponent()

```jsx
mockComponent(
     componentClass,
     [mockTagName]
)
```
传入一个模拟（mocked）componet 模块，以这种方法来增强它的有用方法，允许它作为一个虚拟的（dummy）React component。而不是像通常那样渲染，这个component 将称为一个简单的`div`（或者是其它标签如果`mockTagName` 被提供）包含任意提供的子节点。

### isElement()

```jsx
isElement(element)
```
如果`element` 是任意的React element，返回`true`。

### isElementOfType()

```jsx
isElementOfType(
     element,
     componentClass
)
```
如果`element` 是一个React 的`componentClass` 类型的element，返回`true`。

### isDOMComponent()

```jsx
isDOMComponent(instance)
```
如果`instance` 是一个DOM component（例如`<div>` 或`<span>`），返回`true`。

### isCompositeComponent()

```jsx
isCompositeComponent(instance)
```
如果`instance` 是一个用户自定义的component，例如一个类或者函数，返回`true`。

### isCompositeComponentWithType()

```jsx
isCompositeComponentWithType(
     instance,
     componentClass
)
```
如果`instance` 是一个React 的`componentClass` 类型的component，返回`true`。

### findAllRendererTree()

```jsx
findAllRendererTree(
     tree,
     test
)
```
遍历（traverse）`tree` 中的所有component，并累积（accumulate）所有`test(component)` 是`true` 的component。这不对它自己有用，但是它为其他测试工具是主要的。

### scryRenderedComponentsWithClass()

```jsx
scryRenderedComponentsWithClass(
     tree,
     className
)
```
在已经渲染的树中搜索所有的component 的DOM element ，其类名匹配`className` 的DOM components。

### findRenderedComponentWithClass()

```jsx
findRenderedComponentsWithClass(
     tree,
     classname
)
```
像[srcyRenderedDOMComponentsWithClass()](https://facebook.github.io/react/docs/test-utils.html#scryrendereddomcomponentswithclass) 类似但是期待这里只有一个结果，并且返回这个结果，如果这里除了这一个还有其它匹配结构，将会抛出异常。

### srcyRenderedComponentsWithTag()

```jsx
srcyRenderedComponentsWithTag(
     tree,
     tagName
)
```
在已经渲染的树中搜索所有的component 的DOM element ，其标签名匹配为`tagName` 的DOM components。

### findRenderedComponentWithTag()

```jsx
findRenderedComponentWithTag(
     tree,
     tagName
)
```
像[srcyRenderedComponentsWithTag()](https://facebook.github.io/react/docs/test-utils.html#scryrendereddomcomponentswithtag) 类似但是期待这里只有一个结果，并且返回这个结果，如果这里除了这一个还有其它匹配结构，将会抛出异常。

### srcyRenderedComponentsWithType()

```jsx
srcyRenderedComponentsWithType(
     tree,
     componentClass
)
```
搜索所有component的类型等于`componentClass` 的实例。

### findRenderedComponentWithType()

```jsx
findRenderedComponentWithType(
     tree,
     componentClass
)
```
像[findRenderedComponentWithType()](https://facebook.github.io/react/docs/test-utils.html#scryrenderedcomponentswithtype) 类似但是期待这里只有一个结果，并且返回这个结果，如果这里除了这一个还有其它匹配结构，将会抛出异常。

## Shallow Rendering

### createRenderer()

```jsx
createRenderer()
```
在你的测试中调用它去创建一个[shallow renderer](https://facebook.github.io/react/docs/test-utils.html#shallow-rendering)。

### shallowRenderer.render()

```jsx
shallowRenderer.render(
     element
)
```
同[ReactDOM.render()]() 类似但是它不需要DOM 并且只渲染一级深度。参考[Shallow Rendering](https://facebook.github.io/react/docs/test-utils.html#shallow-rendering)。


### shallowRenderer.getRenderOutput()

```jsx
shallowRenderer.getRenderOutput()
```
在[shallowRenderer.render()](https://facebook.github.io/react/docs/test-utils.html#shallowrenderer.render) 已经调用之后，返回浅渲染的输出。
