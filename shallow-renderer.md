> 此文章是翻译[Shallow Renderer](https://reactjs.org/docs/shallow-renderer.html)这篇React（版本v16.2.0）官方文档。

## Shallow Renderer

### Importing

```
import ShallowRenderer from 'react-test-renderer/shallow'; // ES6
const ShallowRenderer = require('react-test-renderer/shallow'); // ES5 with npm
```

### 概览

当为React 写单元测试时, 浅渲染会变得十分有用。浅渲染使你渲染组件的“第一层”，并且对组件的 render 方法的返回值进行断言，不用担心子组件的行为，组件并没有实例化或被渲染。浅渲染并不需要 DOM。

例如，如果你有如下的组件：

```
function MyComponent() {
  return (
    <div>
      <span className="heading">Title</span>
      <Subcomponent foo="bar" />
    </div>
  );
}
```

然后你可以断言：

```
import ShallowRenderer from 'react-test-renderer/shallow';

// in your test:
const renderer = new ShallowRenderer();
renderer.render(<MyComponent />);
const result = renderer.getRenderOutput();

expect(result.type).toBe('div')
expect(result.props.children).toEqual([
  <span className="heading">Title</span>,
  <Subcomponent foo="bar" />
]);
```

浅测试当前游戏以讹限制，换句话说就是不支持refs。

> 注意：
> 我们还建议看看Enzyme 的[Shallow Rendering API](http://airbnb.io/enzyme/docs/api/shallow.html)。它在相同的功能上提供了一个更棒的高级 API。


## 参考

### shallowRenderer.render()

你可以把shallowRenderer 想象成一个用来渲染你正在测试的组件的“地方”，并且你可以从那里取到该组件的输出。

`shallowRenderer.render()` 和[ReactDOM.render()](https://reactjs.org/docs/react-dom.html#render)很像。但是它不需要DOM 并且只渲染一层。这就意味着你可以测试组件，不管它们的子组件是怎么实现的。

### shallowRenderer.getRenderOutput()

在`shallowRenderer.render()` 调用之后，你可以使用`shallowRenderer.getRenderOutput()` 得到浅渲染的输出。

然后你就可以对输出进行断言了。
