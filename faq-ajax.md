> 此文章是翻译[AJAX and APIs](https://reactjs.org/docs/faq-ajax.html)这篇React（版本v16.2.0）官方文档。

## AJAX and APIs

### 我如何使用AJAX 请求

在React 中你可以使用任何你喜欢的AJAX 库。像很常用的[Axios](https://github.com/axios/axios)，[jQuery AJAX](https://api.jquery.com/jQuery.ajax/)，以及浏览器内置的[window.fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)。

### 我应该在组件的哪个生命周期中使用AJAX 请求

你应该在[componentDidMount](https://reactjs.org/docs/react-component.html#mounting) 生命周期方法中设置通过AJAX 请求到的数据。这样你可以使用`setState` 去更新你的组件，当数据被检索到的时候。

### 例子：使用AJAX 结果去设置local state

下面这个例子展示了在`componentDidMount` 中使用AJAX 请求去设置组件的local state。

例子中的API 返回如下格式的JSON 对象
```js
{
  items: [
    { id: 1, name: 'Apples', price: '$2' },
    { id: 2, name: 'Peaches', price: '$5' }
  ]
}
```

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      error: null,
      isLoaded: false,
      items: []
    };
  }

  componentDidMount() {
    fetch("https://api.example.com/items")
      .then(res => res.json)
      .then(
        (result) => {
          this.setState({
            isLoaded: true,
            items: result.items
          });
        },
        // Note: it's important to handle erros here
        // instead of a catch() block so that we don't swallow
        // exceptions from actual bugs in component.
        (error) => {
          this.setState({
            isLoaded: true,
            error
          });
        }
      )
  }

  render() {
    const { error, isLoaded, items } = this.state;
    if (error) {
      return <div>Error: {error.message} </div>
    } else if (!isLoaded) {
      return <div>Loading...</div>
    } else {
      return (
        <ul>
          {items.map(item => (
            <li key={item.name}>
              {item.name} {item.price}
            </li>
          ))}
        </ul>
      );
    }
  }
}
```

### 取消

注意如果在一个AJAX 请求完成之后将组件卸载，你可能会看到`cannot read property 'setState' of undefined` 警告。如果这是一个问题，你可以追踪AJAX 请求并在`componentWillUnmount` 生命周期方法中取消它。
