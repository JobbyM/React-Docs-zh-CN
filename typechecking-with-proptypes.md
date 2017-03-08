> 此文章是翻译[Typechecking With PropTypes](https://facebook.github.io/react/docs/typechecking-with-proptypes.html)这篇React（版本v15.4.0）官方文档。

## Typechecking With PropTypes

随着你的应用的增大， 你可以通过类型检测（typechecking）获取许多bugs。对于一些应用，你可以使用JavaScript 后缀例如[Flow] ()或者[TypeScript]() 去检测你的整个应用。但是即使你不使用这些，在React 有一些内置的类型检测能力。为了能够在component 的props 上运行类型检测，你可以将其赋于`propTypes` 属性（property）：
```jsx
class Greeting extends Component {
  render(){
    return (
      <h1>Hello , {this.props.name}</h1>
    )
  }
}

Greeting.propTypes = {
  name : React.PropTypes.string
}
```
`React.PropTypes` 导出一系列的验证器（validator）可以用来确认你收到有效的数据。在这个例子中，我们使用`React.PropTypes.string`。当一个无效的值被提供给prop，一个警告就会在JavaScript console 中显示。为了性能原因，`propTypes` 只在开发模式进行验证。

### React.PropTypes

这有一个例子展示如何使用不同验证器：
```jsx
MyComponent.propTypes = {
  // You can declare that a prop is a specific JS primitive. By default, these
  // are all optional.
  optionalArray: React.PropTypes.array,
  optionalBool: React.PropTypes.bool,
  optionalFunc: React.PropTypes.func,
  optionalNumber: React.PropTypes.number,
  optionalObject: React.PropTypes.object,
  optionalString: React.PropTypes.string,
  optionalSymbol: React.PropTypes.symbol,

  // Anything that can be rendered: numbers, strings, elements or an array
  // (or fragment) containing these types.
  optionalNode: React.PropTypes.node,

  // A React element.
  optionalElement: React.PropTypes.element,

  // You can also declare that a prop is an instance of a class. This uses
  // JS's instanceof operator.
  optionalMessage: React.PropTypes.instanceOf(Message),

  // You can ensure that your prop is limited to specific values by treating
  // it as an enum.
  optionalEnum: React.PropTypes.oneOf(['News', 'Photos']),

  // An object that could be one of many types
  optionalUnion: React.PropTypes.oneOfType([
    React.PropTypes.string,
    React.PropTypes.number,
    React.PropTypes.instanceOf(Message)
  ]),

  // An array of a certain type
  optionalArrayOf: React.PropTypes.arrayOf(React.PropTypes.number),

  // An object with property values of a certain type
  optionalObjectOf: React.PropTypes.objectOf(React.PropTypes.number),

  // An object taking on a particular shape
  optionalObjectWithShape: React.PropTypes.shape({
    color: React.PropTypes.string,
    fontSize: React.PropTypes.number
  }),

  // You can chain any of the above with `isRequired` to make sure a warning
  // is shown if the prop isn't provided.
  requiredFunc: React.PropTypes.func.isRequired,

  // A value of any data type
  requiredAny: React.PropTypes.any.isRequired,

  // You can also specify a custom validator. It should return an Error
  // object if the validation fails. Don't `console.warn` or throw, as this
  // won't work inside `oneOfType`.
  customProp: function(props, propName, componentName) {
    if (!/matchme/.test(props[propName])) {
      return new Error(
        'Invalid prop `' + propName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  },

  // You can also supply a custom validator to `arrayOf` and `objectOf`.
  // It should return an Error object if the validation fails. The validator
  // will be called for each key in the array or object. The first two
  // arguments of the validator are the array or object itself, and the
  // current item's key.
  customArrayProp: React.PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
    if (!/matchme/.test(propValue[key])) {
      return new Error(
        'Invalid prop `' + propFullName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  })
};
Requiring Single Children
With React.PropTypes.element you can specify that only a single child can be passed to a component as children.

class MyComponent extends React.Component {
  render() {
    // This must be exactly one element or it will warn.
    const children = this.props.children;
    return (
      <div>
        {children}
      </div>
    );
  }
}

MyComponent.propTypes = {
  children: React.PropTypes.element.isRequired
};
```

### Requiring Single Children

使用`React.PropTypes.element` 你可以配置仅允许单个子节点传给component：
```jsx
class MyComponent extends Component {
  render(){
    // This must be exactly one element or it will warn.
    const children = this.props.children
    return (
      <div>
        {children}
      </div>
    )
  }
}

MyComponent.PropTypes = {
  children: React.PropTypes.element.isRequired
}
```

### Default Prop Values

通过为你的`props` 与一个特殊的`defaultProps` 属性来定义一个默认值：
```jsx
class Greeting extends Component {
  render(){
    return (
      <h1>Hello , {this.props.name}</h1>
    )
  }
}

// Specifies the default values for props:
Greeting.defaultProps = {
  name: 'Stranger'
}

// Renders "Hello, Stranger"
ReactDOM.render(
  <Greeting />,
  document.getElementById('root')
)
```
这个`defaultProps` 将会确认这个`this.props.name` 有值即使它没有被父component 配置。这个`propType` 将在`defaultProps` 被解析之后进行类型检测，所以类型检测也可用在`defaultProps` 上。
