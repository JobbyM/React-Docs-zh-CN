> 此文章是翻译[Typechecking With PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html)这篇React（版本v16.2.0）官方文档。

## Typechecking With PropTypes

> 注意：

>`React.PropTypes` 从React v15.5 版本已经移入一个不同的包。请使用[`prop-types`库替代](https://www.npmjs.com/package/prop-types)


随着你的应用的增大， 你可以通过类型检测（typechecking）捕获许多bugs。对于一些应用，你可以使用JavaScript 扩展例如[Flow](https://flowtype.org/)或者[TypeScript](https://www.typescriptlang.org/) 去检测你的整个应用。但是即使你不使用这些，在React 有一些内置的类型检测能力。为了能够在组件的props 上运行类型检测，你需要配置特殊的`propTypes` 属性：

```
import PropTypes from 'prop-types';

class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello , {this.props.name}</h1>
    );
  }
}

Greeting.propTypes = {
  name : PropTypes.string
};
```

`PropTypes` 导出一系列的验证器（validator）可以用来确认你收到有效的数据。在这个例子中，我们使用`PropTypes.string`。当一个无效的值被提供给prop，一个警告就会在JavaScript console 中显示。为了性能原因，`propTypes` 只在开发模式进行验证。

### PropTypes

这有一个例子展示如何使用不同验证器：

```
import PropTypes from 'prop-types';

MyComponent.propTypes = {
  // You can declare that a prop is a specific JS primitive. By default, these
  // are all optional.
  optionalArray: PropTypes.array,
  optionalBool: PropTypes.bool,
  optionalFunc: PropTypes.func,
  optionalNumber: PropTypes.number,
  optionalObject: PropTypes.object,
  optionalString: PropTypes.string,
  optionalSymbol: PropTypes.symbol,

  // Anything that can be rendered: numbers, strings, elements or an array
  // (or fragment) containing these types.
  optionalNode: PropTypes.node,

  // A React element.
  optionalElement: PropTypes.element,

  // You can also declare that a prop is an instance of a class. This uses
  // JS's instanceof operator.
  optionalMessage: PropTypes.instanceOf(Message),

  // You can ensure that your prop is limited to specific values by treating
  // it as an enum.
  optionalEnum: PropTypes.oneOf(['News', 'Photos']),

  // An object that could be one of many types
  optionalUnion: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number,
    PropTypes.instanceOf(Message)
  ]),

  // An array of a certain type
  optionalArrayOf: PropTypes.arrayOf(PropTypes.number),

  // An object with property values of a certain type
  optionalObjectOf: PropTypes.objectOf(PropTypes.number),

  // An object taking on a particular shape
  optionalObjectWithShape: PropTypes.shape({
    color: PropTypes.string,
    fontSize: PropTypes.number
  }),

  // You can chain any of the above with `isRequired` to make sure a warning
  // is shown if the prop isn't provided.
  requiredFunc: PropTypes.func.isRequired,

  // A value of any data type
  requiredAny: PropTypes.any.isRequired,

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
  customArrayProp: PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
    if (!/matchme/.test(propValue[key])) {
      return new Error(
        'Invalid prop `' + propFullName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  })
};
```

### Requiring Single Children

使用`React.PropTypes.element` 你可以配置仅允许单个子节点传给组件：

```
import PropTypes from 'prop-types';

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

MyComponent.PropTypes = {
  children: PropTypes.element.isRequired
};
```

### Default Prop Values

通过为你的`props` 配置一个特殊的`defaultProps` 属性来定义一个默认值：

```
class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello , {this.props.name}</h1>
    );
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

如果你正在用像[transform-class-properties](https://babeljs.io/docs/plugins/transform-class-properties/) Babel 转换器，你也可以在React 组件类中声明`defaultProps` 作为静态属性。这个语法还没有最终通过，在浏览器中需要一步编译工作。更多信息，查看[class fields proposal](https://github.com/tc39/proposal-class-fields)。

```
class Greeting extends React.Component {
  static defaultProps = {
    name: 'stranger'
  }

  render() {
    return (
      <div>Hello, {this.props.name}</div>
    )
  }
}
```

这个`defaultProps` 将会确认这个`this.props.name` 有值即使它没有被父组件配置。这个`propType` 将在`defaultProps` 被解析之后进行类型检测，所以类型检测也可用在`defaultProps` 上。
