> 此文章是翻译[Test Renderer](https://reactjs.org/docs/test-renderer.html)这篇React（版本v16.2.0）官方文档。

## Test Renderer

### Importing

```
import TestRenderer from 'react-test-renderer'; // ES6
const TestRenderer = require('react-test-renderer'); // ES5 with npm
```

### 概览

该包提供了一个不需要依赖于DOM 和原生移动环境， 就可以将React 组件渲染成纯 JavaScript 对象的React 渲染器。

本质上，该包可以在无需使用浏览器或[jsdom](https://github.com/tmpvar/jsdom) 的情况下，轻松地抓取由React DOM 或React Native 组件渲染出的平台视图层次结构（类似于DOM树）快照。

例如：

```
import TestRenderer from 'react-test-renderer';

function Link(props) {
  return <a href={props.page}>{props.children}</a>
}

const testRenderer = TestRederer.create(
  <Link page="https://www.facebook.com/">Facebook</Link>
);

console.log(testRenderer.toJSON());

// { type: 'a',
//  props: { href: 'https://www.facebook.comm/' },
//  children: [ 'Facebook' ]}
```

你可以使用Jest 的快照测试功能来自动保存一个该JSON 树文件的副本，并且在你的测试中检查它是否被更改。[了解更多](http://facebook.github.io/jest/blog/2016/07/27/jest-14.html)。

你也可以通过遍历输出去查找特殊节点，进行断言。

```
import TestRenderer from 'react-test-renderer';

function MyComponent() {
  return (
      <div>
        <SubComponent foo="bar" />
        <p className="my">Hello</p>
      </div>
  )
}

function SubComponent() {
  return (
      <div>
        <p className="sub">Sub</p>
      </div>
  )
}

const testRenderer = TestRederer.create(<MyComponent />);
const testInstance = testRenderer.root;

expect(testInstance.findByType(SubComponent).props.foo).toBe('bar');
expect(testInstance.findByProps({className: "sub"}).children).toEqual(['Sub']);
```

### TestRenderer

* TestRenderer.create()

### TestRenderer instance

* testRenderer.toJSON()
* testRenderer.toTree()
* testRenderer.update()
* testRenderer.unmount()
* testRenderer.getInstance()
* testRenderer.root


### testInstance

* testInstance.find()
* testInstance.findByType()
* testInstance.findByProps()
* testInstance.findAll()
* testInstance.findAllByType()
* testInstance.findAllByProps()
* testInstance.instance
* testInstance.type
* testInstance.props
* testInstance.parent
* testInstance.children

## 参考

### TestRenderer.create()

```
TestRenderer.create(element, options);
```

通过传来的React 元素创建一个`TestRenderer` 的实例。它并不使用真实的DOM，但是它依然将组件树完整地渲染到内存，所以你可以对它进行断言。返回的实例拥有以下的方法和属性。

### testRenderer.toJSON()

```
testRenderer.toJSON()
```

返回一个表示渲染后的树的对象。该树仅包含特定平台的节点，像`<div>` 或`<View>` 和他们的属性（props），但是并不包含任何用户编写的组件。这对于[快照测试](http://facebook.github.io/jest/docs/en/snapshot-testing.html#snapshot-testing-with-jest)非常方便。

### testRenderer.toTree()

```
testRenderer.toTree()
```

返回一个表示渲染后的树的对象。不同于`toJSON()` ，它表示的内容比`toJSON()` 提供的内容要更加详细，并且包含用户编写的组件。除非您正在测试渲染器（test rendererer）之上编写你自己的断言库，否则你可能并不需要这个方法。

### testRenderer.update()

```
testRenderer.update(element)
```

使用新的根元素重新渲染内存中的树。它模拟根元素的一次React更新。如果新的元素和之前的元素有相同的type 和key，该树将会被更新；否则，它将重挂载一个新树。

### testRenderer.unmount()

```
testRenderer.unmount()
```

卸载内存中的树，触发相应的生命周期事件。

### testRenderer.getInstance()

```
testRenderer.getInstance()
```

返回与根元素相对应的实例, 如果可用的话。如果根元素是函数组件，该方法无效，因为函数组件没有实例。

### testRenderer.root

```
testRenderer.root
```

返回根元素“测试实例”对象，对于断言树中的特殊节点十分有用。你可以使用它来查找其他更深层的“测试实例”。

### testInstance.find()

```
testInstance.find(test)
```

找到一个`test(testInstance)` 返回`true` 的后代测试实例。如果`test(testInstance)` 没有对一个明确的测试实例返回`true` ，将会报错。

### testInstance.findByType()

```
testInstance.findByType(type)
```

找到一个匹配指定`type` 的后代测试实例，如果没有匹配指定的`type` 的一个测试实例，将会报错。

### testInstance.findByProps()

```
testInstance.findByProps(props)
```

找到一个匹配指定`props` 的后代测试实例，如果没有匹配指定的`props` 的一个测试实例，将会报错。


### testInstance.findAll()

```
testInstance.findAll(test)
```

找到所有的`test(testInstance)` 返回`true` 的后代测试实例。

### testInstance.findAllByType()

```
testInstance.findAllByType(type)
```

找到所有的匹配指定`type` 的后代测试实例。

### testInstance.findAllByProps()

```
testInstance.findAllByProps(props)
```

找到所有的匹配指定`props` 的后代测试实例。

### testInstance.instance

```
testInstance.instance
```

该测试实例相对应的组件实例。它只能用于类组件，因为函数组件没有实例。它匹配给定的组件内部的 `this` 的值。

### testInstance.type

```
testInstance.type
```

该测试实例相对应的组件的type，例如，`<Button />` 组件有`Button` type。

### testInstance.props

```
testInstance.props
```

该测试实例相对应的props，例如，`<Button size="small" />` 组件有`{size: 'small'}` 作为props。

### testInstance.parent

```
testInstance.parent
```

该测试实例的父测试实例。

### testInstance.children

```
testInstance.children
```

该测试实例的子测试实例。

## 思考

你可以将`createNodeMock` 函数作为option 传递给`TestRenderer.create`，可以自行模拟refs。`createNodeMock` 接受当前元素作为参数，应该返回一个模拟的 ref 对象。当你要测试一个依赖于refs 的组件时，它十分有用。

```
import TestRederer from 'react-test-renderer';

class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.input = null;
  }
  componentDidMount() {
    this.input.focus();
  }
  render() {
    return <input type="text" ref={el => this.input = el} />
  }
}

let focused = false;
TestRenderer.create(
  <MyComponent />,
  {
    createNodeMock: (element) => {
      if (elementy.type === 'input') {
        // mock a focus function
        return {
          focus: () => {
            focusd = true;
          }
        };
      }
      return null;
    }
  }
);
expect(focused).toBe(true);
```
