> 此文章是翻译[Introducing JSX](https://reactjs.org/docs/introducing-jsx.html)这篇React（版本v16.2.0）官方文档。

## Introducing JSX

考虑这个变量声明：

```
const element = <h1>Hello , world!</h1>;
```

这个古怪的标签语法既不是字符串也不是HTML。

它就是JSX，它是JavaScript 的一个语法扩展。我们建议在React 中使用它去描述UI 的展示。JSX 可能会使你记起模版语言，但是它使用JavaScript 的所有功能。

JSX 产生React 的“element”。我们将在[下一章](https://reactjs.org/docs/rendering-elements.html) 研究如何将它们渲染为DOM。接下来，你会了解对你使用React 所需要的基本的JSX 知识。

### Why JSX？

React 包含这样一个事实，即渲染逻辑本质上与其他UI 逻辑耦合：事件如何处理，state 如何随时间变化，以及如何准备数据以显示。

相反，通过将标记和逻辑放在单独的文件中，而不是人为地分离技术，React 使用称为“组件”的松散耦合单元包含它们实现[关注点分离（Sepatation of concerns）](https://en.wikipedia.org/wiki/Separation_of_concerns)。[在之后的章节中](https://reactjs.org/docs/components-and-props.html)，我们将回到到组件中，但是如果你还不习惯在js 中添加标记，那么[这个讨论](https://www.youtube.com/watch?v=x7cQ3mrcKaY)可能会让你信服。

React [不需要](https://reactjs.org/docs/react-without-jsx.html)用JSX，但大多数人觉得它有助于视觉辅助当UI 工作在JavaScript代码时。它还允许作出React 以显示更多有用的错误和警告消息。

既然如此，我们开始吧！

### Embedding Expressiongs in JSX

你可以在JSX 中，通过大括号来嵌入任何[JavaScript expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Expressions)。

例如，`2+2`，`user.name` 和`formatName(user)` 都是有效的表达式：

```
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

[在CodePen 上尝试](https://reactjs.org/redirect-to-codepen/introducing-jsx)

为了可读性我们将JSX 拆分成多行。虽然它不是强制的，当在拆分JSX 为多行时，我们仍然建议使用括号去包括它，避免[automatic semicolon insertion](http://stackoverflow.com/q/2846283) 陷阱。

### JSX is an Expression Too

编译之后，JSX 表达式变成了常规的JavaScript 函数调用，并且对JavaScript 对象求值。

也就是说，你可以使用JSX，在`if` 语句和`for` 循环中，将它赋给一个变量，作为一个参数接受它，并从funcitons 中返回：

```
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger</h1>;
}
```

### Specifying Attributes with JSX

你可以使用引号来指定字符串特性：

```
const element = <div tabIndex="0"></div>;
```

在特性中，你也可以在通过使用大括号来包裹JavaScript 表达式：

```
const element = <img src={user.avatarUrl}></img>
```

在特性中嵌入JavaScript 表达式时，不要在大括号上放置引号。 你应该使用引号（对于字符串值）或花括号（用于表达式），但不能同时使用在同一特性。

>**警告：**

>由于JSX 更接近JavaScript 而不是HTML，React DOM 使用`camelCase` 属性命名约定，而不是HTML 特性名字。

>例如，`class` 在JSX 中变成了[className](https://developer.mozilla.org/en-US/docs/Web/API/Element/className),`tabindex` 变成了[tabIndex](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/tabIndex)

### Specifying Children with JSX

如果一个标签为空，你应该使用`/>` 来闭合标签，类似XML：

```
const element = <img src={user.avatarUrl} />;
```

JSX 标签可以包含子节点：

```
const element = (
  <div>
    <h1>Hello </h1>
    <h2>Good to see you here.</h2>
  </div>
)；
```

### JSX Prevents Injection Attacks

在JSX 中嵌入用户输入也是安全的：

```
const title = response.potentiallyMaliciousInput
// This is safe
const element = <h1>{title}</h1>
```

默认，在渲染之前React DOM [转义](http://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-on-html)在JSX 中嵌入的所有值。因此它能确保你永远不能注入任何没有在应用程序明确写入的东西。在渲染之前所有的东西都被转成字符串。这有助于防止(XSS（跨站脚本）)[https://en.wikipedia.org/wiki/Cross-site_scripting]攻击。

### JSX Represents Objects

Babel 将JSX 编译成通过`React.createElement()` 的方法调用：

下面两个例子是完全一样的：

```
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

```
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

`React.createElement()` 执行一些检测来帮助你写出没有bug 的代码，但实际上它创建了如下一个对象：

```
// Note: this structure is simplified
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world'
  }
}
```

这些对象被称为"React elements"。你可以把它们想成描述你想要在屏幕上看的。React 读取这些对象并使用它们构成DOM 并保证它们更新。

在下一章中我们将继续探索渲染React 元素为DOM。

> **Tip:**

>我们建议在你的编辑器中[“Babel” 语言定义](http://babeljs.io/docs/editors) 选项，以至于ES6 和JSX 代码正确高亮。本网站使用[Oceanic Next](https://labs.voronianski.com/oceanic-next-color-scheme/) 颜色主体来兼容它。
