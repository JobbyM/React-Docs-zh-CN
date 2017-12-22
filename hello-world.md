> 此文章是翻译[Hello World](https://reactjs.org/docs/hello-world.html)这篇React（版本v15.5.4）官方文档。

## Hello World

使用[this Hello World example code on CodePen](https://reactjs.org/redirect-to-codepen/hello-world) 是最简单的开始React 的方式。你不必安装任何东西；你只需要跟着我们在新页面打开它就可以了，跟着我们浏览这个例子。如果你更喜欢使用本地开发环境，请学习[Installationg](https://reactjs.org/docs/installation.html) 页面。

这个最简单的React 例子：

```
ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
);
```

它渲染了一个标题头"Hello World" 在页面上。

接下来的部分将向你逐渐介绍使用React。我们将会检测构建React  应用的块：元素和组件。一旦你掌握它们，你就可以从小的复用块构建复杂的应用。

### A Note on JavaScript

React 是一个JavaScript 库，所以假设你已经掌握了基本的JavaScript 语言。如果你不觉得非常有信心，我们建议你[更新JavaScript 知识](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)，你可以更容易的学习。

在例子中，我们将会使用一些ES6 语法。因为它相对来说是新的，所以我们会保守的使用它，但是我们鼓励你去了解[arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，[classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)，[template literals](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals)，[let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let) 和[const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const) 语句。你可以使用[Babel REPL](https://babeljs.io/repl/#?presets=react&code_lz=MYewdgzgLgBApgGzgWzmWBeGAeAFgRgD4AJRBEAGhgHcQAnBAEwEJsB6AwgbgChRJY_KAEMAlmDh0YWRiGABXVOgB0AczhQAokiVQAQgE8AkowAUPGDADkdECChWeASl4AlOMOBQAIgHkAssp0aIySpogoaFBUQmISdC48QA) 去了解ES6 代码如何编译。
