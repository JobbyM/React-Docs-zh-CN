> 此文章是翻译[Fragments](https://doc.react-china.org/docs/fragments.html)这篇React（版本v16.2.0）官方文档。

## Fragments

React 中一个常见模式是为一个组件返回多个元素。Fragments 可以让你组织一个子元素列表，不在DOM 中增加额外节点。

```
render() {
  return (
    <React.Fragment>
      <ChildA />
      <ChildB />
      <ChildC />
    </React.Fragment>
  );
}
```

还有一个新的[简短语法](https://reactjs.org/docs/fragments.html#short-syntax)来声明它们，但它却不被所有流行工具支持。

### Motivation

一个常见模式是为一个组件返回一个子元素列表。以这个示例的React 片段为例：

```
class Table extends React.Component {
  render() {
    return (
      <table>
        <tr>
          <Columns />
        </tr>
      </table>
    );
  }
}
```

`<Columns />` 需要返回多个`<td>` 元素，为了渲染的HTML 有效的，。如果一个父div 用在 `<Columns />` 的`render()` 函数里面，那么最终的HTML 将是无效的。

```
class Columns extends React.Component {
  render() {
    return (
      <div>
        <td>Hello</td>
        <td>World</td>
      </div>
    );
  }
}
```

在`<Table />` 输出的结果是：

```
<table>
  <tr>
    <div>
      <td>Hello</td>
      <td>World</td>
    </div>
  </tr>
</table>
```

所以，我们引入`Fragment`。

### Usage

```
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}
```

导致正确的`<Table />` 输出的结果：

### Short Syntax

有一种新的较短的语法，你可以用来声明fraggments。它看起来像空标签：

```
class Columns extends React.Component {
  render() {
    return (
      <>
        <td>Hello</td>
        <td>World</td>
      </>
    );
  }
}
```

你可以像使用其它元素那样使用`<></>`，除了它不能接受key 或特性。

请注意，[许多工具还不支持它](https://reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html#support-for-fragment-syntax)，因此你可能需要显式地写`<React.Fragment>` 直到工具赶上为止。

### Keyed Fragments

Fragments 使用明确的`<React.Fragment>` 语法声明可以有key。一个使用场景是映射一个集合为fragment 数组 -- 例如，去创建一个描述列表：

```
function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        // Without the `key`, React will fire a key warning
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}
```

`key` 是唯一能够传递给`Fragment` 的特性。未来，我们可以添加额外的特性，例如事件处理器。

### Live Demo

你可以在[CodePen](https://codepen.io/reactjs/pen/VrEbjE?editors=1000) 上尝试新的JSX framgment 语法。
