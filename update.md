> 此文章是翻译[Immutability Helpers](https://facebook.github.io/react/docs/update.html)这篇React（版本v15.4.0）官方文档。

## Immutability Helpers

>**注意：`update` 是历史遗留的add-ons。使用[kolodny/Immutability-helper](https://github.com/kolodny/immutability-helper)来替代。**

**Importing**

```jsx
import update from 'react-addons-update'; // ES6
var update = require('react-addons-update'); // ES5 with npm
var update = React.addons.update; // ES5 with react-with-addons.js
```

## Overview

React 让你使用你想要的任何数据管理类型，包括变化。然而，如果你在你应用的性能关键的地方使用不可变的数据，它很容易实现一个快速的[shouldComponentUpdate()](https://facebook.github.io/react/docs/react-component.html#shouldcomponentupdate)方法去显著地加速你的应用。

在JavaScript 中处理不可变的数据比本身语言就为不可变数据设计的更困难，例如[Clojure](http://clojure.org/)。然而，我们提供一个简单的不可变的助手，`update()`，它可以更容易地处理这种类型的数据，而没有根本上改变你的数据是如何展示的。你也可以查看Facebook 的[Immutable-js](https://facebook.github.io/immutable-js/docs/) 其中的[Advanced Performance](https://facebook.github.io/react/docs/advanced-performance.html) 章节部分获取更多关于Immutable-js 的细节。

### The Main Idea

如果你有如下可变数据：
```jsx
myData.x.y.z = 7;
// or ...
myData.a.b.push(9);
```
你没有方法判断那个数据已经发生改变，虽然之前的副本已经被重写了。相反，你需要创建`myData` 新的副本并且只改变它需要改变的部分。然后你可以比肩`myData` 旧的副本同新的副本在`shouldComponentUpdate()` 使用完全相等（triple-equals）：
```jsx
const newData = deepCopy(myData);
newData.x.y.z = 7;
newData.a.b.push(9);
```
不幸的是，深度复制（deep copies）花费大，并且有时是不可能的。你可以通过仅复制需要改变的对象已经重用还没有改变的对象来减轻此功能。不幸的是，在今天的JavaScript z中这仍然是麻烦的：
```jsx
const newData = extend(myData, {
     x: extend(myData.x, {
          y: extend(myData.x.y, {z: 7}),
     }),
     a: extend(myData.a, {b: myData.a.b.concat(9)})
});
```
虽然这是相当高效的（因为它值对`log n` 个对象进行浅复制，并且复用剩下的），书写是非常痛苦的。注意所有的重复！这不仅恼人，而且提供了一个大面积的bugs。

### update()

`update()` 围绕这一模式提供了简单的语法糖，使编写这些代码更容易。代码变成：
```jsx
import update from 'react-addons-update';

const newData = update(myData, {
     x: {y: {z: {$set: 7}}},
     a: {b: {$push [9]}}
})
```
虽然语法需要花费一点时间去习惯（它的灵感来自于[MongoDB's query language]()）这没有冗余，这是静态分析的，并且它同变化版本没有太多的打字。

`$-` 前缀的关键字称为命令。它们正在“改变（mutating）”的数据结构称为目标（target）。

## Available Commands

* `{$push: array}` 在目标上`push()`所有的`array` 中的条目。
* `{$unshifit: arrray}` 在目标上`unshift` 所有的`array` 中的条目。
* `{$splice: array of arrays}` 在目标上为`arrays` 中的每一个条目调用`splice()` 方法，使用有条目提供的参数。
* `{$set: any}` 整体替换这个目标。
* `{$merge: object}` 同目标合并`object` 中的key。
* `{$apply: function}` 将当前值传入函数并且使用新返回的值更新它。

## Examples

### Simple push

```jsx
const initialArray = [1, 2, 3];
const newArray = update(initialArray, {$push: [4]}); // => [1, 2, 3, 4]
```
`initialArray` 仍然是`[1, 2, 3]`。

### Nested collections

```jsx
const collection = [1, 2, {a: [12, 17, 15]}];
const newCollection = update(collection, {2: {a: {$splice: [[1, 1, 13, 14]]}}});
// => [1, 2, {a: [12, 13, 14, 15]}]
```
访问`collection` 的索引`2`，键值`a`, 执行splice 从索引`1`（移除`17`）同时插入`13` 和`14`

### Updating a value based on its current one

```jsx
const obj = {a: 5, b: 3};
const newObj = update(obj, {b: {$apply: function(x){return x* 2;}}});
// => {a: 5, b: 6}
// 这是相等的，但是对深度嵌套的集合是冗余的
const newObj2 = update(obj, {b: {$set: obj.b * 2}})
```

### (Shallow) Merge

```jsx
const obj = {a: 5, b: 3};
const newObj = update(obj, {$merge: {b: 6, c: 7}}); // => {a: 5, b: 6, c: 7}
```
