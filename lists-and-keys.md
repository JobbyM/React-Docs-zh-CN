> 此文章是翻译[lists-and-keys](https://facebook.github.io/react/docs/lists-and-keys.html)这篇React（版本v15.4.0）官方文档。

## Lists and Keys

首先，让我们复习一下在JavaScript 中如何改变数组。

给定代码如下，我们使用`map()`函数来接受一个`numbers` 数组并对其值进行加倍。我们将`map()`函数的返回值赋给`doubled` 并打印这个值：
```jsx
const numbers = [1, 2, 3, 4, 5]
const doubled = numbers.map((number)=> number*2)
console.log(doubled)
```
代码输出值为`[2, 4, 6, 8, 10]`。

在React 中，改变[elements](https://facebook.github.io/react/docs/rendering-elements.html) 数组的值几乎是一样的。

### Rendering Multiple Components

你可以构建elements 集合，在JSX 中（[include them in JSX](https://facebook.github.io/react/docs/introducing-jsx.html#embedding-expressions-in-jsx)）使用大括号`{}` 来包含它们。

下面，我们通过JavaScript `map()` 方法来循环遍历一个`numbers` 数组。为数组每一项返回一个`<li>` element。最后，我们将遍历结果赋值给`listItem`：
```jsx
const numbers = [1, 2, 3, 4, 5]
const listItem = numbers.map((number)=>
  <li>{number}</li>
)
```
我们将其全部`listItems` 数组包裹在`<ul>`element 中，然后将其渲染到DOM 上（[render it to the DOM](https://facebook.github.io/react/docs/rendering-elements.html#rendering-an-element-into-the-dom)）：
```jsx
ReactDOM.render(
  <ul>{listItem}</ul>,
  document.getElementById('root')
)
```
这段代码展示从1 到5 的数字列表

### Basic List Component

通常你会在一个组件（[component](https://facebook.github.io/react/docs/components-and-props.html)）中渲染一个列表。

我们重构之前的例子为组件，此组件接受一个`numbers` 数组，输出也一个elements 的无须列表
```jsx
function NumberList(props){
  const numbers = props.numbers;
  const listItems = numbers.map((number)=>
    <li>{number}</li>
  )
  return (
    <ul>{listItems}</ul>
  )
}
const numbers = [1, 2, 3, 4, 5]
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
)
```
当你运行这段代码，会有一个警告提示，其需要一个key。一个"key" 作为一个重要的特性在创建的elements。在下一节，我们来讨论为什么它非常重要。

让我们在`numbers.map()` 中赋予一个`key` 来解决这个key 问题。
```jsx
function NumberList(props){
  const numbers = props.numbers;
  const listItems = numbers.map((number)=>
    <li key={number.toString()}>{number}</li>
  )
  return (
    <ul>{listItems}</ul>
  )
}
const numbers = [1, 2, 3, 4, 5]
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
)
```

### Keys

Keys 帮助React 确认列表中哪一项发生改变，被添加或者是被移除。在数组中需要给elements 一个稳定的身份（identify）
```jsx
const numbers = [1, 2, 3, 4, 5]
const listItem = numbers.map((number)=>
  <li key={number.toString()}>
    {number}
  </li>
)
```
最好的提供一个唯一标志的字符串在其兄弟节点中。大多数情况下你使用数据中的IDs 作为keys：
```jsx
const todoItemss = todos.map((todo)=>
  <li key={todo.id}>
    {todo.text}
  </li>
)
```
当你不需要为渲染像提供IDs 时，你可以使用项目索引作为最后的手段（a last resort）：
```jsx
const todoItemss = todos.map((todo, index)=>
  // Only do this if items have no stable IDs
  <li key={index}>
    {todo.text}
  </li>
)
```
如果数据能够被重新排列，我们不建议使用索引作为keys，因为它会变慢。如果感兴趣建议阅读[in-depth explanition about why keys are necessary](https://facebook.github.io/react/docs/reconciliation.html#recursing-on-children)。

### Extracting Components with Keys

Keys 只有在数组的上下文中才有意义。

例如，如果你提取了一个`ListItem` 组件，你应该在`<ListItem />` 数组的elements 上使用这个key，而不是在`ListItem` 中的`<li>` element 上。

错误的使用Key 的例子：
```jsx
function ListItem(props){
  const value = props.value
  return (
    // Wrong! There is no need to specify hte key here:
    <li key={value.toString()}>
      {value}
    </li>
  )
}

function NumberList(props){
  const numbers = props.numbers
  const listItems = numbers.map((number)=>
    // Wrong! The key should have been specified here:
    <ListItem value={number}/>
  )

  return(
    <ul>
      {listItems}
    </ul>
  )
}
const numbers = [1, 2, 3, 4, 5]

ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
)
```
正确使用Key 的例子：
```jsx
function ListItem(props){
  const value = props.value
  return (
    // Correct! There is no need to specify the key here:
    <li>
      {value}
    </li>
  )
}

function NumberList(props){
  const numbers = props.numbers
  const listItems = numbers.map((number)=>
    // Correct! Key should be specified inside the array
    <ListItem key={number.toString()}
      value={number}/>
  )

  return(
    <ul>
      {listItems}
    </ul>
  )
}
const numbers = [1, 2, 3, 4, 5]

ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
)
```
一个好方法是在调用个`map()` 方法的elements 内部使用keys。

### Keys Must Only Be Unique Among Sibings

Keys 在使用数组中必须是唯一的。即时它们不是全局唯一。我们可以使用相同的keys 在不同的数组中：
```jsx
function Blog(props){
  const sidebar = (
    <ul>
      {props.posts.map((post)=>
        <li key={post.id}>
          {post.title}
        </li>
      )}
    </ul>
  )

  const content = props.posts.map((post)=>
    <div key={post.id}>
      <h3>{post.title}</h3>
      <p>{post.content}</p>
    </div>
  )

  return (
    <div>
      {sidebar}
      <hr />
      {content}
    </div>
  )
}


const posts = [
  {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},
  {id: 2, title: 'Installation', content: 'You can install React from npm.'}
]
ReactDOM.render(
  <Blog posts={posts} />,
  document.getElementById('root')
)
```
Keys 只是作为React 的线索它们不能传递给你的组件。如果你必须在你的组件中使用同样的值，请明确将其传给一个不同名字的props：
```jsx
const content = posts.map((post)=>
  <Post
    key={post.id}
    id={post.id}
    />
)
```
在上述例子中，这个`Post` 组件只能读取`props.id` 而不能读取`props.key`

### Embedding map() in JSX

在上述例子中我们声明了一个不同`listItems` 变量在JSX 中。
```jsx
function NumberList(props){
  const numbers = props.numbers
  const listItems = numbers.map((number)=>
    <ListItem key={number.toString()}
      value={number}/>
  )

  return(
    <ul>
      {listItems}
    </ul>
  )
}
```
JSX 允许在大括号里嵌入任何表达式[embedding any expression](https://facebook.github.io/react/docs/introducing-jsx.html#embedding-expressions-in-jsx)，所以我们可以在一行`map()` 结果。
```jsx
function NumberList(props){
  const numbers = props.numbers
  return (
    <ul>
      {numbers.map((number)=>
        <ListItem key={number.toString()}
          value={number} />
      )}
    </ul>
  )
}
```
有时在这清晰代码中结果，但是这种样式可能被滥用。像在JavaScript 中，它只是由你来决定提取一个变量是否更可读。记住如果`map()` 体中太嵌套了，提取一个组件（[extract a component](https://facebook.github.io/react/docs/components-and-props.html#extracting-components)）是更好的方法。
