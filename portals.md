> 此文章是翻译[Portals](https://reactjs.org/docs/portals.html)这篇React（版本v16.2.0）官方文档。

## Portals

Portal 提供最好的方式去渲染子节点到父组件DOM 层级之外的DOM 节点上。

```
ReactDOM.createPortal(child, container)
```

第一个参数（`child`）是任何[能够渲染的React 子节点](https://reactjs.org/docs/react-component.html#render)，像元素、字符串、或者fragment。第二个参数（`container`）是一个DOM 元素。

### Usage

通常讲，当你从组件的render 方法返回一个元素，该元素仅能加载到离其最近的父节点的子元素的DOM：

```
render() {
  // React mounts a new and renders the children into it
  return (
    <div>
      {this.props.children}
    </div>
  )
}
```

然而，有时候将其插入到子节点到DOM 的不同位置也是有用的：

```
render() {
  // React does *not* create a new div. It renders the children into `domNode`.
  // `domNode` is any valid DOM node, regardless of its location in the DOM.
  return ReactDOM.createPortal(
    this.props.children,
    domNode,
  );
}
```

对于portal 的一个典型用例是当父组件有`overflow: hidden` 或`z-index` 样式，但你需要子组件能够在视觉上“跳出（break out）”其容器。例如，对话框、hovercards以及提示框。

>**注意：**

>记住这点非常重要，当在使用portals时，你需要确保遵循合适的可访问指南。

[在CodePen 上尝试](https://codepen.io/gaearon/pen/yzMaBd)。

### Event Bubbling Through Portals

即使portal 可以在DOM 树中的任何位置，它的行为都像普通的React 子节点。无论子节点是否为portal，类似于context 的功能都是完全相同的，因为无论DOM 树中的位置如何，portal 仍然存在于React 树中。

这包括事件冒泡。从portal 内部触发的事件将传播到包含React 树中的祖先，即使这些元素不是DOM 树中的祖先。假设以下HTML 结构：

```
<html>
  <body>
    <div id="app-root"></div>
    <div id="modal-root"></div>
  </body>
</html>
```

在`#app-root` 里的`Parent` 组件能够捕获到一个未被捕获的，从兄弟节点`#modal-root` 冒泡上来的事件。

```
// These two containers are siblings in the DOM
const appRoot = document.getElementById('app-root');
const modalRoot = document.getElementById('modal-root');

class Modal extends React.Component {
  constructor(props) {
    super(props);
    this.el = document.getElementById('div');
  }

  componentDidMount() {
    // The portal element is inserted in the DOM tree after
    // the Modal's children are mounted, meaning that children
    // will be mouted on a detached DOM node. If a child
    // compoennt requires to be attached to the DOM tree
    // immediately when mounted, for example to measure a
    // DOM node, or uses 'autoFocus' in a descendant, add
    // state to Modal and only render the children when Modal
    // is inserted in the DOM tree.
    modalRoot.appendChild(this.el);
  }

  componentWillUnmount() {
    modalRoot.removeChild(this.el);
  }

  render() {
    return ReactDOM.createPoral(
      this.props.children,
      this.el,
    );
  }
}

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {clicks: 0};
    this.handleClick = this.handleClick.bind(this);
  }

  handlClick() {
    // This will fire when the button in child is clicked,
    // updating Parent's state, even though button
    // is not direct descendant in the DOM.
    this.setState(prevState => ({
      clicks: prevState.clicks + 1
    }));
  }

  render() {
    return (
      <div onClick={this.handleClick}>
        <p>Number of clicks: {this.state.clicks}</p>
        <p>
          Open up the browser DevTools
          to observe that the Button
          is not a child of the div
          with the onClick handler.
        </p>
        <Modal>
          <Child />
        </Modal>
      </div>
    );
  }
}

function Child() {
  // The click event on this button will bubble up to parent,
  // because there is no 'onClick' attribute defined
  return (
    <div className="modal">
      <button>Click</button>
    </div>
  );
}

ReactDOM.render(<Parent />, appRoot);
```

[在CodePen 上尝试](https://codepen.io/gaearon/pen/jGBWpE)。

在父组件里捕获一个来自portal 的事件冒泡，允许不固有的依赖于portal 的更为灵活的抽象的开发。例如，若你在渲染一个`<Modal />` 组件，父组件能够捕获其事件而无论其是否采用portal 实现。
