> 此文章是翻译[Test Utilities](https://reactjs.org/docs/test-utils.html)这篇React（版本v16.2.0）官方文档。

## Test Utilities

### Importing

```
import ReactTestUtils from 'react-dom/test-utils'; // ES6
const ReactTestUtils = require('react-dom/test-utils'); // ES5 with npm
```

### 概览

`ReactTestUtils` 在你所选择的测试框架便于测试React 组件。在Facebook，我们使用[Jest](https://facebook.github.io/jest/) 来无痛的JavaScript测试。学习如何开始Jest，通过Jest 的[网站React 教程](http://facebook.github.io/jest/docs/en/tutorial-react.html#content)。

> 注意：
> Airbnb 已经发布了一个测试工具称为Enzyme，它很容易维护，操作，和遍历你的React 组件的输出。如果你决定在一个单元测试工具使用一起Jest，或任何其他的测试运行，可以查看这里：http://airbnb.io/enzyme/

* Simulate
* renderIntoDocument()
* mockComponent()
* isElement()
* isElementOfType()
* isDOMComponent()
* isCompositeComponent()
* isCompositeComponentWithType()
* findAllInRenderedTree()
* scryRendererDOMComponentsWithClass()
* findRendererDOMComponentWithClass()
* scryRendererDOMComponentsWithTag()
* findRendererDOMComponentWithTag()
* scryRendererComponentsWithTag()
* findRendererComponentWithTag()

## 参考

### 浅渲染（Shallow Rendering）

当编写React 的单元测试时，浅渲染可能很有帮助。通过浅渲染，可以渲染组件“一个深度”，并断言关于其渲染方法返回的内容的事实，而不必担心未实例化或渲染的子组件的行为。这不需要DOM。

> 注意：
> 浅渲染已经移动到`react-test-renderer/shallow`。[在浅渲染的参考页了解更多](https://reactjs.org/docs/shallow-renderer.html)


### 其他工具

#### Simulate

```
Simulate.{eventName}(
  element,
  [eventData]
)
```

模拟DOM 节点上的事件分发，使用可选的`eventData` 事件数据。

`Simulate` 对[每一个React 能理解的事件](https://reactjs.org/docs/events.html#supported-events) 都有一个方法。

**点击一个元素**

```
// <button ref={(node) => this.button = node}>...</button>
const node = this.button;
ReactTestUtils.Simulate.click(node)
```

**改变输入表单域的值，然后按回车**

```
// <input ref={(node) => this.textInput = node}/>
const node = this.textInput;
node.value = 'giraffe';
ReactTestUtils.Simulate.change(node);
ReactTestUtils.Simulate.keyDown(node, {key: "Enter", keyCode: 13, which: 13});
```

> 注意：
> 你必须提供你在React 组件中使用的任何事件属性（例如，keyCode、which 等），因为反React 不会为你创建任何事件属性。

#### renderIntoDocument()

```
renderIntoDocument(element)
```

渲染一个React 元素到文档中一个派遣的DOM 节点中。**这个方法需要一个DOM**。

> 注意：
> 在导入React 之前, 你需要`window`，`window.document` 和`window.document.createElement` 全局有效。否则，Reacti 会认为它无法访问DOM，而`setState` 之类的方法将无法工作。

#### mockComponent()

```
mockComponent(
  componentClass,
  [mockTagName]
)
```

传入一个模拟的组件模块给这个方法来增强它，使用有用的方法去允许它作为一个dummy React 组件。不像通常的渲染，组件将成为一个简单的`<div>`（或其他`mockTagName` 标签）包含任何已提供的子节点。

> 注意：
> `mockComponent()` 是一个遗留API。我们推荐使用[浅渲染](https://reactjs.org/docs/test-utils.html#shallow-rendering) 或[jest.mock()](https://facebook.github.io/jest/docs/en/tutorial-react-native.html#mock-native-modules-using-jestmock)

#### isElement()

```
isElement(element)
```

如果`element` 是任意React 元素，返回`true`。

#### isElementOfType()

```
isElementOfType(
  element，
  componentClass
)
```

如果`element` 是一个`componentClass` 类型的React 元素，返回`true`。

#### isDOMComponent()

```
isDOMComponent(instance)
```

如果`instance` 是DOM 组件（例如`<div>` 或`<span>`），返回`true`。

#### isCompositeComponent()

```
isCompositeComponent(instance)
```

如果`instance` 是用户自定义组件，例如class 或function，返回`true`。

#### isCompositeComponentWithType()

```
isCompositeComponentWithType(
  instance，
  componentClass
)
```

如果`instance` 是一个`componentClass` React 组件，返回`true`。

#### findAllInRenderedTree()

```
findAllInRenderedTree(
  tree,
  test
)
```

遍历`tree` 中的所有组件并累积所有组件，其`test(component)` 是`true`。这对自己没有用，但它作为一种原始的其他测试工具是有用的。

#### scryRendererDOMComponentsWithClass()

```
scryRendererDOMComponentsWithClass(
  tree,
  className
)
```

在渲染树中查找所有DOM 组件的类名为`className` 的DOM元素组件。


#### findRendererDOMComponentWithClass()

```
findRendererDOMComponentWithClass(
  tree,
  className
)
```

同[scryRendererDOMComponentsWithClass](https://reactjs.org/docs/test-utils.html#scryrendereddomcomponentswithclass) 类似，但是期望只有一个结果，并返回这个结果，如果不是有且仅有一个，则抛出异常。

#### scryRendererComponentsWithTag()

```
scryRendererComponentsWithTag(
    tree,
    tagName
)
```

在渲染树中查找所有DOM 组件的标签名为`tagName` 的DOM元素组件。

#### scryRendererComponentWithTag()

```
scryRendererComponentWithTag(
  tree,
  className
)
```

同[scryRendererComponentsWithTag](https://reactjs.org/docs/test-utils.html#scryrendereddomcomponentswithtag) 类似，但是期望只有一个结果，并返回这个结果，如果不是有且仅有一个，则抛出异常。

#### scryRendererComponentsWithType()

```
scryRendererComponentsWithType(
    tree,
    componentClass
)
```

查找所有的类型为`componentClass` 的组件实例。

#### scryRendererComponentWithType()

```
scryRendererComponentWithType(
  tree,
  className
)
```

同[scryRendererComponentWithType](https://reactjs.org/docs/test-utils.html#scryrenderedcomponentswithtype) 类似，但是期望只有一个结果，并返回这个结果，如果不是有且仅有一个，则抛出异常。
