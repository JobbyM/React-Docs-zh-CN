> 此文章是翻译[Reconciliation](https://reactjs.org/docs/reconciliation.html)这篇React（版本v16.2.0）官方文档。

## Reconciliation

React 提供声明式（declarative）API 所以你不必担心每次更新的变化。这使得写应用更加容易，但在React 中如何实现它可能并不明显。本文解释了我们选择的React 的比较算法（“diffing”algorithm ）使得组件的更新变得可预见，同时对于高性能应用来说足够快。

### Motivation

当你使用React，在某一点你可以认为`render()` 函数创建了React 元素树。在下一个state 或props 更新时，`render()` 函数将会返回一个不同的React 元素树。React 需要了解如何高效地更新UI 去匹配最新的树（the most recent tree）。

这有一些通用的方法来处理将一棵树转换成另一个树时产生最少的操作的算法问题。然后，这个算法[state of the art algorithm](http://grfia.dlsi.ua.es/ml/algorithms/references/editsurvey_bille.pdf)是复杂度为O(n3) ，其中n 为树中元素的个数。

如果我们在React 中使用这个方法，这是1000 个元素将需要10 亿次比较。这个花费太大了。相反，React 实现了一个基于两个假设而实现的一个启发式（heuristic）O(n)算法：

1. 不同类型的元素将会产生不同的树。
2. 开发人员可以使用一个`key` prop 来指示在不同的渲染中那个元素可以保持稳定。

事实上，这些假设在几乎所有的用例中都是有效的。

### The Diffing Algorithm

当比较不同的两个树，React 首先比较两个根元素。根据根元素的类型不同，它有不同的行为。

#### Elements Of Different Types

当根元素有不同的类型，React 将会销毁旧的树并从头创建新的树。从`<a>`到`<img>`或者从`<Article>`到`<Comment>`或者从`<Button>`到`<div>` -- 这些都将导致全部重新构建。

当销毁一个树，旧的DOM 节点也会被销毁。组件实例会收到`componentWillUnmout()`。当构建一个新树，新的DOM 节点将会被插入DOM。组件实例将会接受`componentWillMount()` 之后接受`componentDidMount()`。所有和旧树关联的state 都会失去。

在根下的所有组件都会被卸载，它们的state 也会被销毁。例如，当比较：

```
<div>
  <Counter />
</div>

<span>
  <Counter />
</span>
```

这会销毁旧的`Counter`，并重新加载一个新的。

#### DOM Elements Of The Same Type

当比较两个相同类型的React 元素时，React 检查它们的特性，保留相同的底层DOM 节点，只更新改变的特性。例如：

```
<div className="before" title="stuff" />

<div className="after" title="stuff" />
```

通过比较这两个元素，React 知道只需要修改底层DOM 节点的`className`。

在更新`style` 时，React 也知道只需要更新发生改变的属性。例如：

{% raw %}
```
<div style={{color: 'red', fontWeight: 'bold'}} />

<div style={{color: 'green', fontWeight: 'bold'}} />
```
{% endraw %}

在改变这两个元素时，React 知道只需要修改`color` 样式，而不需要修改`fontWeight`。

处理DOM 节点之后，React 开始的递归（recurse）孩子节点。

#### Component Elements Of The Same Type

当一个组件更新，而实例保持不变，那么这个state 在渲染之间仍然保留。React 更新底层组件 的props 去匹配新元素，需要在底层实例上调用`componentWillReceiveProps()` 和`componentWillUpdate()` 方法。

下一步，`render()` 方法被调用，比较算法递归之前的结果和现在的结果。

#### Recursing On Children

默认情况下，当对DOM 节点的子节点进行递归时，React 只会同时遍历两个子列表，并在有差异时生成一个变异。

例如，当在孩子节点的末尾添加一个元素，这两棵树之间的转换效果很好：

```
<ul>
  <li>first</li>
  <li>second</li>
</ul>

<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
```

React 将会匹配两个`<li>first</li>`树，匹配比较两个`<li>second</li> ` 树，然后插入`<li>third</li> ` 树。

如果你缺乏经验的实现它，在一开始插入一个元素将会有一个糟糕的性能。例如， 这两棵树的的转换不佳：

```
<ul>
  <li>Duke</li>
  <li>Villanova</li>
</ul>

<ul>
  <li>Connecticut</li>
  <li>Duke</li>
  <li>Villanova</li>
</ul>
```

React 将会改变每一个子节点而没有意识到需要完好无损的保留`<li>Duke</li>` 和`<li>Villanova</li>` 两个子树。这种低效是一个问题。

#### Keys

为了解决这个问题，React 支持一个`key` 特性。当子节点有了`key`，React 使用这个`key` 去比较原来的树的子节点和之后树的子节点。例如，添加一个`key` 到我们上面那个低效的例子中可以使树的转换变高效：

```
<ul>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>

<ul>
  <li key="2014">Connecticut</li>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>
```

现在React 知道key 为`2014` 的元素是新的，`2015`和`2015` 源只需要移动就可以了。

实际上，找到一个key 通常不难。你将要展示的这个元素可能已经有了唯一一个ID，所以这个key 可以来自你的数据：

```
<li key={item.id}>{item.name}</li>
```

如果不是这样，你可以添加一个新的ID 特性到你的数据模型，或者是hash 一部分内容生成一个key。这个key 需要在它的兄弟节点中是唯一的就可以了，不需要是全局唯一。

作为最后的手段，你可以将数组中的索引作为key。如果它们从不重新排序，它们工作也很好，但是如果重新排序，将是缓慢的。

当索引用作keys 时，重新排序也会导致组件state 问题。组件实例根据其key 进行更新和重用。如果key 是索引，则移动项会更改它。因此，诸如受控输入的组件stae 可能会以意外的方式被混淆和更新。

[这是](https://reactjs.org/redirect-to-codepen/reconciliation/index-used-as-key)一个示例，说明在CodePen 上使用索引作为key 引起的问题 ，[这是](https://reactjs.org/redirect-to-codepen/reconciliation/no-index-used-as-key)是同一个示例的更新版本，演示如何不使用索引作为key 来解决这些重新排序、排序和前面的问题。

### Tradeoffs

重要的是要记住，这个reconciliation 算法的实现细节。React 可以在每次操作时重新渲染整个程序
；最后的结果都是相同的。为了明确起见，在这个上下文中，reerender 意味着调用所有组件的渲染，但这并不意味着React 将卸载和重新装入它们。它将只适用前几节所述规则的差异。

我们经常改善这个启发式为了使常用的例子更快。在当前的实现中，你可以表达一个子树在其同级中移动的事实，但是你不能告诉它已经移动到其他地方。则该算法将重新呈现该完整子树。

在当前实现中，你可以表达这样一个事实，子树已经在兄弟节点之间移动，但是你不必告诉被移到什么位置。这个算法将会重新渲染整个子树。

因为React 依赖这个启发式，如果它们背后的假设没有得到满足，性能将会受到影响。

1. 这个算法不会尝试匹配不同组件 类型的子树。如果你看到在两个组件类型之间有相似的输出，你可以使它们有相同的类型。在实践中，我们还没有发现这是一个问题。

2. Keys 应该是稳定的、可预测的和唯一的。不稳定的keys（像`Math.random()`生成的）将会导致许多组件实例和DOM 节点不必要的重复创建，这将会导致性能的下降以及失去子节点中的state。
