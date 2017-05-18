> 此文章是翻译[Integrating with Other Libraries](https://facebook.github.io/react/docs/integrating-with-other-libraries.html)这篇React（版本v15.5.4）官方文档。

## Integrating with Other Libraries

React可用于任何Web应用程序。 它可以嵌入到其他应用程序中，并且稍微小心，其他应用程序可以嵌入到React中。 本指南将研究一些更常见的用例，重点是与[jQuery](https://jquery.com/)和[Backbone](http://backbonejs.org/)的集成，但是可以将相同的想法应用于将组件与任何现有代码集成。

### Integrating with DOM Manipulation Plugins

React不知道对React之外的DOM所做的更改。 它根据自己的内部表示来确定更新，如果相同的DOM节点被另一个库操作，则React会变得困惑，无法恢复。

这并不意味着将React与影响DOM的其他方式结合起来是不可能的，甚至一定是难以理解的，你只需要注意每个库在做什么。

避免冲突的最简单方法是防止React组件更新。 你可以通过渲染React无法更新的元素来执行此操作，如空的`<div />`。

### How to Approach the Problem

为了演示这个，我们来绘制一个通用的jQuery插件的包装器。

我们将附加一个[ref](https://facebook.github.io/react/docs/refs-and-the-dom.html)到根DOM元素。 在`componentDidMount`里面，我们将得到一个引用，所以我们可以把它传递给jQuery插件。

为了防止在加载后React触摸DOM，我们将从`render()`方法返回一个空的`<div />`。 `<div />`元素没有属性或子元素，所以React没有任何理由更新它，让jQuery插件可以自由地管理DOM的一部分：
```jsx
class SomePlugin extends React.Component {
  componentDidMount() {
    this.$el = $(this.el);
    this.$el.somePlugin();
  }

  componentWillUnmount() {
    this.$el.somePlugin('destroy');
  }

  render() {
    return <div ref={el => this.el  = el} />;
  }
}
```
请注意，我们定义了`componentDidMount`和`componentWillUnmount`[lifecycle hooks](https://facebook.github.io/react/docs/react-component.html#the-component-lifecycle)。 许多jQuery插件将事件监听器附加到DOM，因此在`componentWillUnmount`中分离它们很重要。 如果插件没有提供清理方法，你可能必须提供自己的方法，记住要删除任何事件侦听器，以防止内存泄漏。

### Integrating with jQuery Chosen Plugin

有关这些概念的更具体的例子，让我们为插件[Chosen](https://harvesthq.github.io/chosen/)编写一个最小的包装器，它增加了`<select>`的输入。

> 注意：
只是因为这是可能的，并不意味着它是React应用程序的最佳方法。 我们鼓励你可以使用React component。 React component在React应用程序中更容易重用，并且通常可以更好地控制其行为和外观。

首先，我们来看看Chosen对DOM做了什么。

如果你在`<select>` DOM node上调用它，它将从原始DOM node读取属性，以内联样式隐藏，然后在`<select>`之后附加具有自己的视觉表示的单独的DOM node。 之后它会触发jQuery事件以通知我们有关更改。

假设这是我们正在使用我们的`<Chosen>`包装器React component所寻求的API。
```jsx
function Example() {
  return (
    <Chosen onChange={value => console.log(value)}>
      <option>vanilla</option>
      <option>chocolate</option>
      <option>strawberry</option>
    </Chose>
  );
}
```
为了简单起见，我们将其作为[uncontrolled component](https://facebook.github.io/react/docs/uncontrolled-components.html)来实现。

首先，我们将使用`render()`方法创建一个空的component，我们返回`<select>`包装在一个`<div>`中：
```jsx
class Chosen extends React.Component {
  render() {
    return (
      <div>
        <select className="Chosen-select" ref={el => this.el = el}>
          {this.props.children}
        </select>
      </div>
    )
  }
}
```
注意我们如何在额外的`<div>`中包装`<select>`。 这是必要的，因为Chosen将在我们传递给它的`<select>`节点之后附加另一个DOM元素。 然而，就React而言，`<div>`总是只有一个孩子。 这是我们如何确保React更新不会与Chosen附加的额外DOM node冲突。 重要的是，如果你在React流之外修改DOM，则必须确保React没有理由触摸这些DOM node。

接下来，我们将实现lifecycle hooks。 我们需要在`componentDidMount`中通过ref 指向的`<select>`节点来初始化Chosen，在`componentWillUnmount`进行销毁：
```jsx
componentDidMount() {
  this.$el = $(this.el);
  this.$el.chosen();
}

componentWillUnmount() {
  this.$el.chosen('destroy');
}
```
[在CodePen 上尝试](http://codepen.io/gaearon/pen/qmqeQx?editors=0010)

请注意，React对`this.el`字段没有任何特殊的含义。 它之所以能起作用，因为我们之前已经在`render()`方法中的ref中指定了这个字段：
```jsx
<select className="Chosen-select" ref={el => this.el = el}>
```
这足以让我们的component呈现，但是我们也希望得到关于值变化的通知。 为此，我们将订阅由Chosen管理的`<select>`上的jQuery更改事件。

我们不会将`this.props.onChange`直接传递给Chosen，因为组件的props可能随着时间的推移而改变，这包括事件处理程序。 相反，我们将声明一个调用`this.props.onChange`的`handleChange()`方法，并将其订阅到jQuery `change`事件：
```jsx
componentDidMount() {
  this.$el = $(this.el);
  this.$el.chosen();

  this.handleChange = this.handleChange.bind(this);
  this.$el.on('change', this.handleChange);
}

componentWillUnmount() {
  this.$el.off('change', this.handleChange);
  this.$el.chosen('destroy');
}

handleChange(e) {
  this.props.onChange(e.target.value);
}
```
[在CodePen 上尝试](http://codepen.io/gaearon/pen/bWgbeE?editors=0010)

最后还有一件事要做。 在React中，props可以随着时间的推移而改变。 例如，如果父component的state改变，`<Chosen>` component可以获得不同的子级。 这意味着在集成点上，重要的是我们手动更新DOM以响应props更新，因为我们不再让React为我们管理DOM。

Chosen的文档表明，我们可以使用jQuery `trigger()`API来通知它关于原始DOM元素的更改。 我们将让React处理在`<select>`内更新`this.props.children`，但是我们还将添加一个`componentDidUpdate()` lifecycle hook，通知Chosen关于children列表中的更改：
```jsx
componentDidUpdate(prevProps) {
  if(prevProps.children !== this.props.children) {
    this.$el.trigger("chosen:updated");
  }
}
```
这样，当React管理的`<select>`子项更改时，Chosen将知道更新其DOM元素。

`Chosen` component 的完整实现如下所示：
```jsx
class Chosen extends React.Component {
  componentDidMount() {
    this.$el = $(this.el);
    this.$el.chosen();

    this.handleChange = this.handleChange.bind(this);
    this.$el.on('change', this.handleChange);
  }

  componentDidUpdate(prevProps) {
    if(prevProps.children !== this.props.children) {
      this.$el.trigger("chosen:updated");
    }
  }

  componentWillUnmount() {
    this.$el.off('change', this.handleChange);
    this.$el.chosen('destroy');
  }

  handleChange(e) {
    this.props.onChange(e.target.value);
  }

  render() {
    return (
      <div>
        <select className="Chosen-select" ref={el => this.el = el}>
          {this.props.children}
        </select>
      </div>
    )
  }
}
```
[在CodePen 上尝试](http://codepen.io/gaearon/pen/xdgKOz?editors=0010)

### Integrating with Other View Libraries

由于[`ReactDOM.render()`](https://facebook.github.io/react/docs/react-dom.html#render)的灵活性，React可以嵌入到其他应用程序中。

虽然React在启动时通常用于将单个根Reac component 加载到DOM中，但`ReactDOM.render()` 也可以多次调用为UI的独立部分，可以像按钮一样小，或者与应用程序一样大。

事实上，这正是在Facebook中如何使用React。 这使我们可以一步一步地在React中编写应用程序，并将其与现有的服务器生成的模板（server-generated）和其他客户端代码（client-side）相结合。

### Replacing String-Based Rendering with React

旧版Web应用程序中的常见模式是将DOM的块（chunks）作为字符串进行描述，并将其插入到DOM中，如：`$el.html(htmlString)`。 代码库（codebase）中的这些要点适用于引入的React。 只需将基于字符串的渲染重写为React component即可。

所以下面的jQuery实现...
```jsx
$('#container').html(`<button id="btn">Say Hello</button>`);
$('#btn').click(function() {
  alert('Hello!');
});
```
...可以使用React component 重写：
```jsx
function Button() {
  return <button id="btn">Say Hello</button>;
}

ReactDOM.render(
  <Button />,
  document.getElementById('container'),
  function() {
    $('#btn').click(function() {
      alert('Hello!');
    });
  }  
);
```
从这里你可以开始将更多的逻辑转移到component中，并开始采用更为常见的React实践。 例如，在component中，最好不要依赖于ID，因为相同的component可以被多次渲染。 相反，我们将使用[React event system](https://facebook.github.io/react/docs/handling-events.html)，并直接在React `<button>`元素上注册点击处理程序：
```jsx
function Button(props) {
  return <button onClick={props.onClick}>Say Hello</button>;
}

function HelloButton() {
  function handleClick() {
    alert('Hello!');
  }
  return <Button onClick={handleClick}/>
}

ReactDOM.render(
  <Button />,
  document.getElementById('container')  
);
```
[在CodePen 上尝试](http://codepen.io/gaearon/pen/RVKbvW?editors=1010)

你可以拥有你喜欢的许多这样的隔离component，并使用`ReactDOM.render()` 将它们渲染到不同的DOM容器。 渐渐地，当你将更多的应用程序转换为React时，你将能够将它们组合成较大的component，并将部分`ReactDOM.render()` 调用到层次结构中。

### Embeddding React in a Backbone View

[Backbone](http://backbonejs.org/) 视图通常使用HTML字符串或字符串生成模板函数（string-producing template functions）为其DOM元素创建内容。 此过程也可以通过渲染一个React component来替代。

下面我们将创建一个名为`ParagraphView`的Backbone视图。 它将覆盖Backbone的`render()` 函数，以将React `<Paragraph>`组件呈现到由Backbone（`this.el`）提供的DOM元素中。 这里也是使用[`ReactDOM.render()`](https://facebook.github.io/react/docs/react-dom.html#render)：
```jsx
function Paragraph(props) {
  return <p>{props.text}</p>
}

const ParagraphView = Backbone.View.extends({
  render() {
    const text = this.model.get('text');
    ReactDOM.render(<Paragraph text={text}/>, this.el);
    return this;
  },
  remove() {
    ReactDOM.unmountComponentAtNode(this.el);
    Backbone.View.prototype.remove.call(this);
  }
});
```
[在CodePen 上尝试](http://codepen.io/gaearon/pen/gWgOYL?editors=0010)

重要的是，我们还在`remove`方法中调用`ReactDOM.unmountComponentAtNode()`，以便在拆分时，React注销与组件树相关联的事件处理程序和其他资源。

当一个组件从React树中删除时，清理将自动执行，但是由于我们正在删除整个树，所以我们必须调用之为此方法。

### Integrating with Model Layers

虽然通常建议使用单向数据流（unidirectional data flow），如[React state](https://facebook.github.io/react/docs/lifting-state-up.html)，[Flux](http://facebook.github.io/flux/)或[Redux](http://redux.js.org/)，但React component可以使用其他框架和库中的模型层。

### Using Backbone Models in React Components

从React component中消费[Backbone](http://backbonejs.org/)模型和集合的最简单方法是收听各种更改事件并手动强制更新。

负责渲染模型的component将监听`'change'`事件，而负责呈现集合的component将监听`'add'`和`'remove'`事件。 在这两种情况下，请调用[`this.forceUpdate()`](https://facebook.github.io/react/docs/react-component.html#forceupdate) 以使用新数据重新渲染组件。

在下面的示例中，`List` component使用`Item`组件渲染单个item来渲染Backbone集合。
```jsx
class Item extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
  }

  handleChange() {
    this.forceUpdate();
  }

  componentDidMount() {
    this.props.model.on('change', this.handleChange);
  }

  componentWillUnmount() {
    this.props.model.off('change', this.handleChange);
  }

  render() {
    return <li>{this.props.model.get('text')}</li>;
  }
}

class List extends React.Component {
  constructor(props) {
    super(props);
    tihs.handleChange = this.handleChange();
  }

  handleChange() {
    this.forceUpdate();
  }

  componentDidMount() {
    this.props.collection.on('add', 'remove', this.handleChange);
  }

  componentWillUnmount() {
    this.props.collection.off('add', 'remove', this.handleChange);
  }

  render() {
    return (
      <ul>
        {this.props.collection.map(model => (
          <Item key={model.cid} model={model} />
        ))}
      </ul>
    );
  }
}
```
[在CodePen 上尝试](http://codepen.io/gaearon/pen/GmrREm?editors=0010)

### Extracting Data from Backbone Models

上述方法需要你的React component了解Backbone模型和集合。如果你以后计划迁移到另一个数据管理解决方案，你可能希望聚焦关于Backbone的知识尽可能少的部分代码。

一个解决方案是将模型的属性作为纯数据每当它改变时提取，并将该逻辑保留在一个单一的位置。以下是将Backbone模型的所有属性提取到state的[高阶组件](https://facebook.github.io/react/docs/higher-order-components.html)，将数据传递到包装component。

这样，只有高阶component需要了解Backbone模型内部部件，并且应用程序中的大多数组件可以与Backbone保持不变。

在下面的例子中，我们将复制模型的属性以形成初始state。我们订阅更改事件（并在卸载时取消订阅），当它发生时，我们使用模型的当前属性更新state。最后，我们确定，如果`model` props本身发生变化，我们不要忘记取消订阅旧模型，并订阅新模型。

请注意，此示例并不意味着与Backbone工作有关的细节，但它应该为你提供如何以通用方式处理这个问题的想法：
```jsx
function connectToBackboneModel(WrappedComponent) {
  return class BackboneComponent extends React.Component {
    constructor(props) {
      super(props);
      this.state = Object.assign({}, props.model.attributes);
      this.handleChange = this.handleChange.bind(this);
    }

    componentDidMount() {
      this.props.model.on('change', this.handleChange);
    }

    componentWillReceiveProps(nextProps) {
      this.setState(Object.assign({}, nextProps.model.attributes));
      if (nextProps.model !== this.props.model) {
        this.props.model.off('change', this.handleChange);
        nextProps.model.on('change', this.handleChange);
      }
    }

    componentWillUnmount() {
      this.props.model.off('change', this.handleChange);
    }

    handleChange(model) {
      this.setState(model.changeAttributes());
    }

    render() {
      const propsExceptModel = Object.assign({}, this.props);
      delete propsExceptModel.model;
      return <WrappedComponent {...propsExceptModel} {...this.state} />;
    }
  }
}
```
为了演示如何使用它，我们将`NameInput` React组件连接到Backbone模型，并在每次输入更改时更新其`firstName`属性：
```jsx
function NameInput(props) {
  return (
    <p>
      <input value={props.firstName} onChange={props.handleChange} />
      <br />
      My name is {props.firstName}.
    </p>
  )
}

const BackboneNameInput = connectToBackboneModel(NameInput);

function Example(props) {
  function handleChange(e) {
    model.set('firstName', e.target.value);
  }

  return (
    <BackboneNameInput
      model={props.model}
      handleChange={handleChange}
    />
  );
}

const model = new Backbone.Model({ firstName: 'Frode' });
ReactDOM.render(
  <Example model={model} />,  
  document.getElementById('root')
);
```
[在CodePen 上尝试](http://codepen.io/gaearon/pen/PmWwwa?editors=0010)

这种技术不限于Backbone。 你可以通过在lifecycle hooks中订阅其更改，并可选地将数据复制到本地React state，将React与任何模型库配合使用。
