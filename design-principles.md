> 此文章是翻译[Design Principles](https://facebook.github.io/react/contributing/design-principles.html)这篇React（版本v15.4.0）官方文档。

## Design Principles

我们写了这篇文档，以至于你可以有一个更好的想法关于我们怎么决定，React 做什么，React 不做什么以及我们的开发理念是什么。虽然我们很高兴看到社区贡献，但是我们不希望选择一个破坏一个或多个这些规则的路径。

>**Note：**
这篇文档假设对React 深入的了解。它描述了React 自身的设计规则，而不是React component 或应用。
作为一个对React 的介绍，查看[Thinking in React](https://facebook.github.io/react/docs/thinking-in-react.html)替代。

### Composition

React 的主要特征是component 的组合（composition）。由不同人编写的components 应该也能一起很好地工作。你可以添加功能到component 而不会引起涟漪性改变在整个代码库中，这对我们是重要的。

例如，它应该可以引入一些state 到component 而不会改变任何component 使用它。同样，可以添加一些初始化和销毁代码到任何component ，在必要时。

在component 中使用state 或声明周期钩子并没有什么不好。像任何强大功能，它们应该适度使用，但是我们没有理由去移除它们。相反，我们认为它们是使React 有用的必要部分。未来，我们可能启用[更多功能性模式](https://github.com/reactjs/react-future/tree/master/07%20-%20Returning%20State)，但是本地state 和生命周期钩将会是该模型的一部分。

Components 经常被描述为“只是函数（just function）”，但是在我们看来，它们需要的不仅仅是有用。在React 中，component 描述任何组合的行为，这包括渲染、生命周期和state。一些外部库，像[Relay](http://facebook.github.io/relay/)利用其他职责如描述数据依赖来增强components。

### Common Abstraction

通常我们[反对添加功能（resist adding features）]()，那些可以在用户区实现的功能。我们不想使用无用的库的代码使你的应用膨胀。然而，也有例外。

例如，如果React 不为本地state 或生命周期钩子提供支持，人们会为它们创建自定义的抽象。当有多个抽象竞争，React 不能强制或利用它们任何一个的属性。它只能使用它们最小的公共特性工作。

这就是为什么有时我们添加功能到React 自身。如果我们注意到许多component 以不兼容或低效的方式实现某些特性，我们可能会倾向于将它们合并到React 中。我们不会轻率地做它。当我们这样做，是因为我们有信心提高抽象层次有利于整个生态系统。State、生命周期钩子、 跨浏览器事件标准化据i时很好的例子。

我们经常同社区讨论这些改提案。你可以找到一些讨论通过位于React 问题追踪器上的“大图（big picture）”标签。

### Escape Hatches

React 是务实的。它是由在Facebook 中编写的产品需求驱动的。虽然它受到像功能性编程这种还没有完全成为主流的范式影响，同官方的不同技能和经验水平的开发者接触是这个项目的一个明确的目标。

如果我们要不赞同一个我们不喜欢的模式，我们的责任是考虑所有现有的用例和[教育社区的替代品（educate the community about the alternatives）](https://facebook.github.io/react/blog/2016/07/13/mixins-considered-harmful.html)之前，我们反对它。如果一些模式对构建应用是有用的，但是很难用声明的方式表述它，我们将为它[提供一个命名式API（provide an imperative API）](https://facebook.github.io/react/docs/more-about-refs.html)。如果我们在许多应用中不能为一些我们发现的必须的事情提供完美的API，我们将提供一个[临时的不够标准的工作API（provide a temporary subpar working API）](https://facebook.github.io/react/docs/context.html)，只要稍后我们能够处理它，并且为未来的改进留一个后门。

### Stability

我们重视API 的稳定性。在Facebook 中，我们有超过2 万个component 使用React。许多其他公司，包括[Twitter](https://twitter.com/) 和[Airbnb](https://www.airbnb.com/)，也是React 的重度用户。这就是为什么我们不愿意去改变公共APIs 或行为。

然而，我们认为“没有变化（nothing change）”意思的稳定性被高估了。它很快就编程停滞了。相反，我们更喜欢“在生产中 被大量使用，当反生改变时，有一个清晰的（最好是自动的）迁移路径。”

当我们废弃一个模式时，我们研究在Facebook 的内部使用并田间废弃警告。它们让我们评估改变的影响。有时如果我们太早了，我们会回滚（back out），并且我们需要更具策略性的思考此刻的代码库是否已经为这次改变准备好了。

如果我们确认这次的改变不会具有太大的破坏性并且迁移策略对所有用例是可行的，我们释放废弃警告到开源社区。我们与Facebook 外的大量React 用户进行紧密接触，我们检测流行的开源项目并且引导它们修复这些废弃。

鉴于Facebook React 庞大规模的代码库，成功的内部迁移通常是一个很好的指标，其余公司也不会遇到问题。然而，有时人们支持其他一些我们还没有遇到的例子，我们将会为他们添加逃生窗口（escape hatch）或者重新思考我们的方法。

我们不会没有任何原因的废弃一些东西。我们意识到有时废弃警告导致挫折，但是我们添加它们是因为废弃清理了路径为改善和新特性 ，我们和社区中的许多人都认为是有价值的。

例如，我们在React 15.2.0 增加了一个[关于未知DOM props 的警告（warning about unknow DOM props）](https://facebook.github.io/react/warnings/unknown-prop.html)。许多项目都会收到影响。然而修复这个警告是重要的，以至于我们能够引入[自定义属性（custom attributes）](https://github.com/facebook/react/issues/140) 支持为React。像这样的原因在每一个我们添加的废弃后面。

当我们添加一个废弃警告，我们保留它在当前主版本，并且[在下一个主版本中改变这个行为（change the behavior in the next major version）](https://facebook.github.io/react/blog/2016/02/19/new-versioning-scheme.html)。如果这涉及到大量的重复手动工作，我们将释放一个[codemod](https://www.youtube.com/watch?v=d0pOgY8__JM)脚本去自动完成大多数改变。codemods能让我们向前移动而不会停滞在大量代码中，并且我们也鼓励你使用它们。

你可以在我们释放的[react-codemode](https://github.com/reactjs/react-codemod) 库中到找到codemods。

### Interoperability

我们将同现有系统的互动放在高价值上并且逐步采用。Facebook 有一个庞大的非React 的代码块。它的网站使用称为XHP 的一个服务器端（server-side）component 系统的混合，内部UI 库在React 和React 自身之前产生。对我们来说，任何产品团队能够[从小特性开始使用React（start using React for a small feature）]() 而不是重写它们的代码。

这就是为什么React 提供逃生舱（escape hatches）去和可变性的模块工作，并且尝试和其它UI 库也能很好的工作。你可以包裹一个已经存在的命令式UI 到一个声明式component，反之亦然。这是逐步采用的关键。

### Scheduling

即使当你的components 被描述为函数，当你使用React 时，你不会直接调用它们。每一个component 返回一个[需要被渲染成什么的描述（description of what need to be rendered）](https://facebook.github.io/react/blog/2015/12/18/react-components-elements-and-instances.html#elements-describe-the-tree)，并且那个描述包裹可以包括像`<LikeButton>` 的用户编写的component 已经像`<div>`的平台特定的component。它是由React 在未来某点“展看（unroll）”`<LikeButton>` 并且按照component 的递归真实地应用改变到这个UI 树上。

这是一个微小的区别但是一个强大的功能。既然你不调用component 的函数，但是让React 调用它，这意味着React 在必要时有能力延迟调用它。在当前实现React 递归遍历这个树，并且在整个更新树的一次tick 中，调用这个渲染函数。然而在未来，它可能开始[为了避免掉帧而延迟一些更新（delaying some updates to avoid droppoing frames）](https://github.com/facebook/react/issues/6170)。

这是React 设计的共同的主题。一些流行的库实现了“推（push）”的方法，当新的数据可用时就进行计算。然而，React 坚持“拉（pull）”的方法，计算可以被推迟非常必要。

React 不是一般的数据处理库。这是一个构建用户界面的库。我们认为它是唯一一个定位在一个应用中去了解现在那一个计算是相关的，那一个不是相关的。

如果一些是在幕后，我们可以推迟和它相关的任何逻辑。如果数据刀疤比帧速率（frame rate）更快，我们可以合并和批量更新。我们可以优先处理来自用户交互的工作（例如有按钮点击导致的一个动画）而不是不重要的后台工作（例如从网络中加载新内容），以避免掉帧。

很明显，我们现在还没有利用这一点。然而这样做的自由就是为什么我们更喜欢控制调度，并且为什么`setState()` 是异步的。从概念上讲，我们认为它是一个“调度更新（scheduling an update）”。

如果我们让用户直接以“退（push）”为基础的范式，通常[在函数性响应编程（Functional Reactive Programming）](https://en.wikipedia.org/wiki/Functional_reactive_programming)中的一些变化，控制调度将会是困难的，对我们来说。我们想要自己拥有这个“胶水（glue）”代码。

这对React 是一个关键目标，大量的用户代码在执行之前，需要返回到最小的React。这能够确保React 保留能够调度，已经分块执行，按照它知道的UI 样子。

团队内部有一个玩笑，React 应该被称为“调度（schedule）”因为React 不想完全成为“响应（reactive）”。

### Develper Experience

提供良好的开发经验对我们很重要。

例如，我们维护[React DevTools](https://github.com/facebook/react-devtools)，它让你在Chrome 和Firefox 中检测React component 树。我们听所，它给Facebook 工程师和社区带来了巨大的生产力提升。

我们也尝试去一个外部的距离提供有用的开发者警告。例如，如果你在开发中，以浏览器不能理解的方式嵌套了标签，React 将警告你，或者如果在API 中写了一个拼写错误。开发者警告和相关的警告是React 开发版本比生产版本慢的主要原因。

我们在Facebook 内部看到的使用模式帮助我们理解常见的错误是什么，以及如何及早防止它们。当我们添加新特性，我们视图遇见常见的错误，并警告它们。

我们一直寻找改善开发者体验的方式。我们很高兴听到你的建议，接受你的贡献，使它更好。

### Debugging

出事的时候，你有面包屑去跟踪位于代码库的错误来源是很重要的。在React 中，props 和state 就是这些面包屑。

如果你在屏幕上看到一些错误，你可以打开React DevTools，找到负责渲染的component，然后看props 和state 是否是正确的。如果它们是正确的，你知道问题发生在component 的`render()` 函数中，或者是被`render()` 调用的一些函数中。这个问题是独立的。

如果state 是错误的，你知道问题是被这个文件中的某一个`setState()` 方法的调用导致的。这也是相对简单的定位和修复，因为通常在一个文件中只有几个`setState()` 调用。

如果props 是错误的，你可以在检测器（inspector）中向上遍历这个树，查找由于向下传递错误rpops 的第一个“毒井（posioned the well）”的component。

这种追踪任何UI 到当前props 和state 的形式下产生数据的能力是对React 是非常重要的。这是一个明确的设计目标，state 不是“困在（trapped）”在闭包和组合器中，直接对React 有用。

虽然UI 是动态的，我们相信，props 和state 转换调试从猜测到枯燥但有限的进程的同步`render()` 函数。我们像保留React 中这个约束，即使它使一些用例，像负责的动画，变得更难。

### Configuration

我们发现全局运行时配置选项是有问题的。

例如，它偶尔要求我们实现像`React.configure(options)` 或`React.register(component)` 的函数。然而，这带来多个问题，并且我们没有意识到好的解决方法。

如果有人调用一些来自第三方的component 的库的这样的函数怎么办？如果一个React 应用程序嵌入另一个React 应用程序，并且它们要求的配置不是兼容的怎么办？一个第三方component 怎么配置它要求的特定的配置？我们认为全局配置同组合不能很好的工作。既然组合是React 的中心，我们不能在代码中提供全局配置。

然而，我们在构建级别上提供一些全局配置。例如，我们提供独立的开发和生产构建。未来我们也[add a profiling build](https://github.com/facebook/react/issues/6627)，并且我们也考虑其他的构建标志。

### Beyond the DOM

我们看到React 的价值，它运行我们编写component 具有很少的bug 并且能很好的组合起来。DOM 是React 最初的渲染目标，但是[React Native]() 对Facebook 和社区有同样的重要。

称为不可知的渲染器对React 设计约束是重要的。它在内部表示中增加了一些开销。相反，任何对核心的转换提升是跨浏览器的。

有一个单个的编程模式，让我们围绕产品形成工程师团队而不是平台。到目前为止，这种权衡（tradeoff）对我没来说是值得的。

### Implementation

我们尝试在任何地方提供优雅的API。我们不关心实现的优雅。现实世界远非完美，在某种程度上，如果这意味着用户不必写它，我们更愿意把丑陋的代码放到库中，当我们评估新的代码，我们正在寻找一个实现是正确的、高效的并且提供一个很好的开发经验。优雅是次要的。

我们喜欢枯燥的代码而不是聪明的代码。代码是一次性的并且经常改变。因此，更重要的是，他[不会引入新的内部抽象，除非绝对必要（doesn
't introduce new internal abstractions unless absolutely necessary）](https://youtu.be/4anAwXYqLG8?t=13m9s)。冗余的代码，容易移动、改变和删除，优雅的代码是过早抽线的并且很难去改变。

### Optimized for Tooling

一些常用的API 有冗长的名字。例如，我们使用`componentDidMount()` 而不是`didMount()` 或者`onMount()`。这是[故意的（intentional ）](https://github.com/reactjs/react-future/issues/40#issuecomment-142442124)。目标是是的交互点同库是高度可见。

在一个像Facebook 庞大的代码库中，能够对特定的API 使用搜索是非常重要的。我们评价不同的冗余的名字，特别是应该谨慎使用的特点。例如`dangerouslySetInnerHTML` 在代码审查中很难出错。

对搜索优化也很重要，因为我们依赖[codemods](https://www.youtube.com/watch?v=d0pOgY8__JM) 做出重大改变。我们希望它是简单的安全的应用大量自动改变在代码库中，并且唯一冗余的明智帮助我们实现它。同样，独特的名字使得写自定义[lint rules](https://github.com/yannickcr/eslint-plugin-react) 关于使用React 而不必担心潜在的误报。

[JSX](https://facebook.github.io/react/docs/displaying-data.html#jsx-syntax) 扮演同样的角色。虽然它不需要React，我们广泛在Facebook 中使用它由于不可知的和务实的原因。

在我们代码库中，JSX 提供一个不明显的暗示对它们处理React element 树。这使得它可能增加构建时间优化像[hoisting constant elements](http://babeljs.io/docs/plugins/transform-react-constant-elements/)，安全的lint 和codemode 的内部component 使用，以及[包括JSX 源代码定位（include JSX source location）](https://github.com/facebook/react/pull/6771) 警告。

### Dogfooding

我们尽力解决社区提出的问题。然而我们有可能优先考虑的问题，在Facebook 内部人们经历的问题。也许反直觉，我们认为这是为什么社区可以打赌在React 上的主要原因。

重度的内部使用给我们信心，React 在明天不会消失。React 在Facebook 内部被创建是为了解决其问题。它为公司带来有形的商业价值，并在许多产品中使用。[Dogfooding](https://en.wikipedia.org/wiki/Eating_your_own_dog_food) 这意味着我们的愿景保持敏锐，我们有一个集中的方向前进。

这不意味着我们忽略来自社区的问题。例如，我们为了React 添加了[web components](https://facebook.github.io/react/docs/webcomponents.html) 和[SVG](https://github.com/facebook/react/pull/6243) 支持，尽管我们内部不依赖它们中的任意一个。我们正在积极[聆听你的痛点（listening to your pian points）](https://github.com/facebook/react/issues/2686) 和并尽我们的能力[解决它（address them）](https://facebook.github.io/react/blog/2016/07/11/introducing-reacts-error-code-system.html)。社区使得React 对我们是特别的，我们很荣幸能够反馈它。

在Facebook 发布了许多开源项目后，我们已经了解到，试图让每一个人都开心的同时，却产生了一个注意力不集中、成长不好的项目。相反，我们选择一个小众的并且专注于使他们高兴带来了积极的净效果。这正是我们使用React 所做的，到目前为止，解决Facebook 产品团队遇到的问题已经很好的反馈到开源社区。

这种方式的缺点是，有时我们不能给予纵狗的关注那些Facebook 团队没有处理的事情，就像“开始（get started）”经验。我们敏锐的意识到这点，我们正在考虑如何以一种对社区中所有人有益的方式提升它，而不会产生我们之前的开源项目中遇到的问题。
