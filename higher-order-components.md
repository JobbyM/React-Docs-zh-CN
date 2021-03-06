> 此文章是翻译[Higher-Order Components](https://reactjs.org/docs/higher-order-components.html)这篇React（版本v16.2.0）官方文档。

## Higer-Order Components

高阶组件（HOC）在React 中是一个复用组件逻辑的高级技术。HOC 本质上不是React API 的一部分。它们是从React 组合性质中出现的模式。

从概念上来说，**高阶组件是一个接受一个组件并返回一个新组件的函数**。

```
const EnhancedComponent = higerOrderComponent(WrappedComponent);
```

鉴于组件转换props 为UI，高阶组件转化一个组件为另一个组件。

HOC 在React 第三方库中是常见的，就像Redux 的[connect](https://github.com/reactjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options) 和Relay 的[createContainer](https://facebook.github.io/relay/docs/api-reference-relay.html#createcontainer-static-method)。

在这篇文档中，我们将讨论高阶组件为什么有用，以及你自己如何去写。

## Use HOCs For Cross-Cutting Concerns

>**注意：**

>我们已经推荐mixins 作为一种处理横切关注点的方式。我们已经意识到mixins 操作的麻烦比它们创造的价值多。[阅读更多](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)关于为什么我们离开mixinx 以及你如何转变你现有的组件。

组件是React 中主要的代码复用单位。然而，你会发现有些模式并不适合传统的组件。

比如说你有一个`CommentList` 组件订阅外部数据源来渲染一个评论列表：

```
class CommentList extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this)
    this.setState = {
      // "DataSource" is some global data source
      comments: DataSource.getComments()
    };
  }

  componentDidMount() {
    // Subscribe the changes
    DataSource.addChangeListener(this.handleChange);
  }

  componentWillUnmount() {
    // Clean up listener
    DataSource.removeChangeListener(this.handleChange);
  }

  handleChange() {
    // Update component state whenever the data source changes
    this.setState({
      comments: DataSource.getComments()
    });
  }

  render() {
    return (
      <div>
        {this.state.comments.map((comment) => (
          <Comment comment={comment} key={comment.id} />
        ))}
      </div>
    );
  }
}
```
之后，你写了一个订阅单个博客文章的组件，它遵循类似的模式：

```
class BlogPost extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.setState = {
      blogPost: DataSource.getBlogPost(props.id)
    };
  }

  componentDidMount() {
    DataSource.addChangeListener(this.handleChange);
  }

  componentWillUnmount() {
    DataSource.removeChangeListener(this.handleChange);
  }

  handleChange() {
    this.setState({
      blogPost: DataSource.getBlogPost(this.props.id)
    });
  }

  render() {
    return <TextBlock blogPost={this.state.blogPost} />;
  }
}
```

`CommentList` 和`BlogPost` 并不相同--它们调用`DataSource` 的不同方法，并且它们渲染不同的输出。但是它们大量的实现是相同的：

* 加载时，在`DataSource` 上添加一个改变监听器。
* 在监听器内部，只要数据源改变就调用`setState`。
* 卸载时，移除这个改变监听器。

你可以设想在一个大的应用中，订阅`DataSource` 以及调用`setState` 相同的模式将会一次又一次发生。我们想要抽象一个允许我们定义这个逻辑在一个地方并且在多个组件中共享它们。这就是高阶组件擅长的地方。

我们可以写一个函数来创建组件，像`CommentList` 和`BlogPost`，订阅`DataSource`。这个函数接受一个子组件，将其作为参数，该子组件接受订阅的数据作为props。让我们来调用这个`withSubscription` 函数：

```
const CommentListWithSubscription = withSubscription(
  CommentList,
  (DataSource) => DataSource.getComments()
);

const BlogPostWithSubscription = withSubscription(
  BlogPost,
  (DataSource, props) => DataSource.getBlogPost(props.id)
);
```
第一个参数是被包裹的组件。第二个参数检索我们感兴趣的数据，通过给定的`DataSource` 和当前的props。

当`CommentListWithSubscription` 和`BlogPostWithSubscription` 被渲染时，`CommentList` 和`BlogPost` 将会被传入检索自`DataSource` 的最新的数据`data` prop：

```
// This function takes a componet...
function withSubscription(WrappedComponent, selectData) {
  // ...and  returns another component...
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      this.state = {
        data: selectData(DataSource, props)
      };
    }

    componentDidMount() {
      // ... that takes care of the subscription...
      DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }

    handleChange() {
      this.setState({
        data: selectData(DataSource, this.props)
      });
    }

    render() {
      // ... and renders the wrapped component with the fresh data!
      // Notice that we pass through any additional props
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}
```

注意HOC 不会修改输入组件，也不会使用继承去复制它的行为。 相反地，HOC 组合原始组件，通过将它包裹在一个容器组件中。HOC 是一个没有副作用的纯函数。

这就是它！这个包裹的组件接受这个容器的所有的props，通过一个新的prop，`data`，它被用作渲染输出。HOC 不管这个数据怎样或为什么被使用，以及这个包裹的组件也不关心这个数据从那里来。

因为`withSubscription` 是一个普通的函数，你可以添加许多或一些你喜欢的参数。例如，你可以希望是`data` 的名称可以配置，以便进一步将HOC 从包裹的组件中独立出来。或者你可以价接受一个配置的`shouldComponentUpdate` 参数，或者一个配置数据源的参数。这些都是可能的，因为HOC 已经完全控制如何定义组件。

像组件，`withSubsription` 和包裹的组件的联系是全部基于props（props-based）的。这使得交换一个HOC 同另一个不同的很简单，只要它们提供相同的props 给包裹的组件。这可能是有用的，如果改变数据获取库，例如。

## Don't Mutable the Original Component. Use Composition.

抵制在HOC 中修改组件的原型（或以其他方式修改）的诱惑。

```
function logProps(InputComponent) {
  InputComponent.proptype.componentWillReceiveProps = function (nextProps) {
    console.log('Current props:', this.props);
    console.log('Next props:', nextProps);
  };
  // The fact that we're returning the original input is a hint that it has
  // been mutated.
  return InputComponent;
}

// EnhancedComponent will log whenever props are received
const EnhancedComponent = logProps(InputComponent);
```
这有几个问题。一个是这个输入组件不能从这个增强组件中分离复用。更严峻的是，如果你应用另一个HOC 到`EnhancedComponent` 上也会改变`componentWillReceiveProps`，第一个HOC 的功能将被重写！这个HOC 也不能适用于没有生命周期方法函数式组件。

更改HOC 是一个有漏洞的抽象--消费者必须知道它们怎样实现来避免和其他HOC 产生冲突。

HOC 应该使用组合，通过包裹输入组件在一个容器组件中，而不是改变：

```
function logProps(WrappedComponent) {
  return class exntends React.Component {
    componentWillReceiveProps(nextProps) {
      console.log('Current props:', this.props);
      console.log('Next props:', nextProps);
    }
    render(){
      // Wraps the input component in a container, without mutating it.
      return <WrappedComponent {...this.props} />
    }
  }
}
```

HOC 同突变版本有相同的功能，同时避免潜在的崩溃。它同类和函数式组件的工作原理相同。并且因为它是纯函数，它可以通过其它HOC 甚至是自己组合。

你可能已经注意到HOC 同 **容器组件（container component）** 的模式相似。容器组件是在高层和底层关注点之间分离责任的策略的一部分。容器管理事情像订阅和state，并且传递props 到组件控制渲染UI 的事情。HOC 使用容器作为它们实现的部分。你可以认为HOC 作为参数化容器组件定义。

## Convention: Pass Unrelated Props Through to the Wrapped Component

HOC 添加特性到组件。它们不应该彻底地改变它的协议。期待从HOC 返回的组件同包裹的组件有相似的接口。

HOC 应该传递props 同它的具体的关注点无关。大多数HOC 包含一个渲染方法，看上去像下面这样：

```
render() {
  // Fiter out extra props that are specific to this HOC and shouldn't be
  // passed through
  const { extraProp, ...passThroughProps} = this.props;

  // Inject props into the wrapped component. These are usually state values or
  // instance methods
  const injectedProp = someStateOrInstanceMethod;

  // Pass props to wrapped component
  return (
    <WrappedComponent
      injectedProp={injectedProp}
      {...passThroughProps}
    />
  );
}
```

这个规则帮助确保HOC 尽可能灵活和可重用。

## Convention: Maximizing Composability

不是所有的HOC 看上去一样。有时它们值接受一个参数，被包裹的组件：

```
const NavbarWithRouter = withRouter(Navbar);
```

通常，HOC 接受额外的参数。在这个来自Relay的例子，一个config 对象被用来配置组件的数据依赖：

```
const CommentWithRelay = Relay.createContainer(Comment, config);
```

HOC 最常见的签名看上去像这样：

```
// React Redux's `connect`
const ConnectedComment = connect(commentSelector, commentActions)(Comment);
```

什么？如果你把它分开，更容易看出正在发生什么。

```
// connect is a function that returns another function
const enhance = connect(commentListSelector, commentListAction);
// The returned function is an HOC, which returns a component that is connected
// to the Redux store
const ConnectedComment = enhance(CommentList);
```

换句话说，`connect` 是一个高阶函数，它返回一个高阶组件！

这种形式可能看上去是困惑的或不必要的，但是它有一个有用的属性。单参数HOC ，如`connect` 函数返回的HOC具有签名`Component => Component`。输出类型同其输入类型相同的函数很容易组合在一起。

```
// Instead of doing this...
const EnhancedComponent = connect(commentSelector)(withRouter(WrappedComponent))

// ... you can use a function composition utility
// compose(f, g, h) is the same as (...args) => f(g(h(...args)))
const enhance = compose(
  // These are both single-argument HOCs
  connect(commentSelector),
  withRouter
)
const EnhancedComponent = enhance(WrappedComponent)
```
（这个相同的属性也允许`connect` 和其它的增强样式HOC 被用作装饰器，一个实验性的JavaScript 提案。）

`compose` 工具函数被许多第三方库提供，包括loadsh（像[`lodash.flowRight`](https://lodash.com/docs/#flowRight)，[Redux](http://redux.js.org/docs/api/compose.html)和[Ramda](http://ramdajs.com/docs/#compose)。

## Convention: Wrap the Display Name for Easy Debugging

被HOC 创建的容器组件在[React Developer Tools](https://github.com/facebook/react-devtools) 展示同任何其他组件一样。为了便于调试，选择一个显示名称，该名称显示（communicate） 是HOC 的结果。

最常用的技术是包裹这个被包裹组件的显示名称。所以如果你的高阶组件被命名为`withSubscription`，并且这个包裹的组件的显示名称是`CommentList`，使用`WitSubscription(CommentList)` 这个显示名称：

```
function withSubscription(WrappedComponent) {
  class WithSubscription extends Component {/* ... */}
  WithSubscription.displayName = `WithSubscription(${getDisplayName(WrappedComponent)})`
  return WithSubscription
}

function getDisplayName(WrappedComponent) {
  return WrappedComponent.displayName || WrappedComponent.name || 'Component';
}
```

## Caveats

高阶组件有一些警告，如果你是一个React 新手，它们不是立即明显的。

### Don't Use HOCs Inside the render Method

React 的diffing algorithm（称为reconciliation）使用组件相等性决定它是否更新已经存在的子树，或者摈弃它，并且加载一个新的。如果从`render` 返回的组件全等于（`===`）之前渲染的组件，React 递归更新子树通过比较它同新的。如果它们不相等，之前的子树被完全卸载。

通常，你应该不需要考虑这个。但是它对HOC 是重要的，因为它意味着你不能应用HOC 到一个组件在一个组件的render 方法内：

```
render() {
  // A new version of EnhancedComponent is created on every render
  // EnhancedComponent1 !== EnhancedComponent2
  const EnhancedComponent = enhance(MyComponent)
  // That cause the entire substree to unmount/remount each time!
  return <EnhancedComponent />
}
```

这的问题不仅关于性能--重新加载组件导致组件的state 和它的孩子的都丢失了。

相反，在组件定义外应用HOC 以至于最后的组件仅被创建一次。那么，它的相等性将是跨渲染器相等。这通常是你想要的，无论如何。

在这些需要你动态应用HOC 的特殊例子中，你也可以在组件的生命周期方法或它的构造器中使用。

### Static Method Must Be Copied Over

有时，在React 组件上定义一个静态方法是有用的。例如，Relay 容器暴露一个静态方法`getFragment` 有利于GraphQL fragments 的组合。

当你应用HOC 到组件上时，虽然，原始的组件被一个容器组件包裹。那意味着新的组件没有原始的组件的任何静态方法。

```   
// Define a static method
WrappedComponent.staticMethod = function() {/* ... */}
// Now apply an HOC
const EnhancedComponent = enhance(WrappedComponent)

// The enhanced component has no static method
typeof EnhancedComponent.staticMethod === 'undefined' // true
```

为了解决它，你应该在返回这个容器之前将方法复制上去：

```
function enhance(WrappedComponent) {
  class Enhance extends Component {/* ... */}
  // Must know exactly which method(s) to copy :(
  Enhance.staticMethod = WrappedComponent.staticMethod
  return Enhance;
}
```

然而，这需要你准确的知道那一个方法需要被复制。你可以使用[hosit-non-react-statics](https://github.com/mridgway/hoist-non-react-statics) 去自动的复制所有非React 的静态方法：

```
import hoistNonReactStatic from 'hosist-non-react-statics';
function enhance(WrappedComponent) {
  class Enhance extends Component {/* ... */}
  hoistNonReactStatic(Enhance, WrappedComponent)
  return Enhance;
}
```

另一个可能的方法是从组件自身单独暴露静态方法。  

```
// Instead of....
MyComponent.someFunction = someFunction
export default MyComponent;

// ...export the method seperately...
export { someFunction };

// ...and in the consuming module, import both
import MyComponent, { someFunction } from './MyComponent.js';
```

### Refs Aren't Passed Through

虽然对高阶组件规则是传递所有的prop 到包裹的组件上，它不可能传递refs。那是因为`ref` 不是一个真正的prop-- 像`key`，它是被React 特殊控制的。如果你添加一个ref 到元素，它的组件是HOC 的结果，ref 引用最外部容器的组件的实例，而不是包裹的组件。

如果你发现你自己面对这个问题，理想的解决方法是找到如何避免使用`ref`。偶尔，对于React 新用户，在这种情况下， 使用prop 将更好，而不是模式依赖refs，

那就是说，有时当refs 是一个必要的安全舱口（escape hatch）-- React 将也不会支持它们。聚焦一个输入域是一个例子，在那你可能想要组件命令式控制。在那个例子，一个解决方法是传递一个ref 回调作为一个普通的props，通过给定一个不同的名称：

```
function Field({ inputRef, ..rest}) {
  return <input ref={inputRef} {...rest} />;
}

// Wrap Field in a higher-order component
const EnhancedField = enhance(Field);

// Inside  a class component's render method...
<EnhancedFiedl
  inputRef={(inputEl) => {
    // This callback gets passed through as a regular prop
    this.inputEl = inputEl
  }}
/>

// Now you can call imperative methods
this.inputEl.foucs()
```

这不是一个完美的解决方案。我们更希望refs 保留一个库关注点，恶如表示要求你手动控制它们。我们正在研究一些方法去解决这个问题，以便使用HOC 是不需要观察。
