> 此文章是翻译[Static Type Checking](https://reactjs.org/docs/static-type-checking.html)这篇React（版本v16.2.0）官方文档。

## Static Type Checking

像[Flow](https://flowtype.org/) 和[TypeScript](https://www.typescriptlang.org/) 的静态类型检查器可以在运行代码之前识别某些类型的问题。 他们还可以通过添加自动完成功能来改善开发人员的工作流程。 出于这个原因，对于更大的代码库我们建议使用Flow 或者TypeScript 来替代`PropTypes`。

### Flow

[Flow](https://flowtype.org/) 是一个针对你的JavaScript 代码的静态类型检查器。它是Facebook 开发的，经常和React 一起使用。 它可以让你使用特殊的类型语法来注释变量，函数和React组件，并尽早地捕获错误。 你可以阅读[introduction to Flow](https://flow.org/en/docs/getting-started/) 来了解它的基本知识。

为了使用Flow，你需要：

* 将Flow 添加到你的项目作为依赖项。
* 确保Flow 语法从编译后的代码中剥离了。
* 添加了类型注释并运行 Flow 来检查它们。

我们将在下面详细解释这些步骤。

#### Adding Flow to a Project

首先，在终端跳转到你的项目目录。你需要运行两条命令。

如果你使用[Yarn](https://yarnpkg.com/)，运行：

```
yarn add --dev flow-bin
yarn run flow init
```

如果你使用[npm](https://www.npmjs.com/)，运行：

```
npm install --save-dev flow-bin
npm run flow init
```

第一个命令安装了最新版的Flow 在你的项目中。第二个命令创建一个你需要提交的Flow 配置文件。

最后，添加`flow` 到你的`package.json` 的`"scripts"` 部分：

```
{
  // ...
  "scripts": {
    "flow": "flow",
    // ...
  },
  // ...
}
```

#### Stripping Flow Syntax from the Complied Code

Flow 通过使用类型注释的特殊的语法扩展了JavaScript 语言。 然而，浏览器并不知道这个语法，所以我们需要确保它最终不会在发送到浏览器的已编译的JavaScript 包中。

实现这一目标的确切方法取决于你使用的编译JavaScript 的工具。

**Create React App**

如果你的项目是使用[Create React App](https://github.com/facebookincubator/create-react-app) 建立的，恭喜！ Flow 注释已经默认剥离，所以在这一步你不需要做任何事情。

**Babel**

>注意：

>这些说明不适用于使用Create React App 的用户。即使Create React App 在底层使用 Babel，它已经被配置为理解Flow。只有那些没有使用Create React App 的用户才需要跟进下面的步骤。

如果你手动为你的项目配置Babel，你将需要为Flow 安装一个特殊的 preset。

如果你使用Yarn，运行：

```
yarn add --dev bable-preset-flow
```

如果你使用npm，运行：

```
npm install --save-dev bable-preset-flow
```

然后将`flow` preset 加入你的[Babel 配置](http://babeljs.io/docs/usage/babelrc/)。比如，如果你通过`.babelrc` 文件配置Babel，它可能会如下所示：

```
{
  "preset": [
    "flow",
    "react"
  ]
}
```

这将会让你在你的代码中使用Flow 语法。

>注意：

>Flow 不需要`react` preset，但是它们经常一起使用。Flow 自身了解JSX 语法，开箱即用。

**Other Build Setups**

如果你不使用Create React App 或Babel，你可以使用[flow-remove-types](https://github.com/flowtype/flow-remove-types) 去跳过类型注解。

#### Running Flow

如果你遵循了上述的说明，你应该能够在第一次就运行Flow。

```
yarn flow
```

如果你使用npm，运行：

```
npm run flow
```

你应该看到像这样一条消息：

```
No errors!
✨  Done in 0.17s.
```

#### Adding Flow Type Annotations

默认，Flow 只检查包含这个注解的文件：

```
// @flow
```

通常它被放置在文件的顶部。 尝试将其添加到项目中的某些文件中，然后运行`yarn flow` 或`npm run flow` 来查看Flow 是否已经发现了一些问题。

也有[一个选择](https://flow.org/en/docs/config/options/#toc-all-boolean)可以强制Flow 不考虑注释检查所有文件. 对于现有的项目它可能太繁琐了，但对于一个新项目如果你想完全用Flow 来书写，那会是合理的。

现在你们都准备好了！我们建议查看以下资源了解更多关于Flow的信息：

* [Flow Documentation: Type Annotations](https://flow.org/en/docs/types/)
* [Flow Documentation: Editors](https://flow.org/en/docs/editors/)
* [Flow Documentation: React](https://flow.org/en/docs/react/)
* [Linting in Flow](https://medium.com/flow-type/linting-in-flow-7709d7a7e969)

### TypeScript

[TypeScript](https://www.typescriptlang.org/) 是一门由Microsoft 开发的编程语言。 它是 JavaScript 的一个类型超集，包含它自己的编译器。 作为一种类型化语言，Typescript 可以早在你的应用程序上线之前，在构建时发现错误和错误。 你可以在[这里](https://github.com/Microsoft/TypeScript-React-Starter#typescript-react-starter)了解更多关于在React 中使用TypeScript 的知识。

为了使用TypeScript， 你需要去：

* 将TypeScript 添加为你的项目的依赖项
* 配置Typescript 编译器选项
* 使用正确的扩展
* 为你使用的库添加定义

让我们详细地检查一下。

#### Adding TypeScript to a Project

一起开始于在终端运行一条命令。

```
yarn add --dev typescript
```

如果你使用[npm](https://www.npmjs.com/)，运行：

```
npm install --save-dev typescript
```

恭喜！你已经将最新版本的TypeScript 安装到你的项目中。 安装TypeScript 让我们可以访问`tsc` 命令。 在配置之前，让我们将`tsc` 添加到`package.json` 中的`"scripts"` 部分：

```
{
  // ...
  "scripts": {
    "build": "tsc",
    // ...
  },
  // ...
}
```

#### Configuring the TypeScript Compiler

除非我们告诉编译器要做什么，否则它对我们将毫无用处。在TypeScript 中，这些规则定义在一个叫`tsconfig.json` 的特殊文件中。运行如下命令生成该文件：

```
tsc --init
```

看看现在生成的`tsconfig.json`，你可以看到有很多选项可以用来配置编译器。 有关所有选项的详细说明，请查看[这里](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)。

在许多选项中，我们会看到`rootDir` 和`outDir`。编译器将以真实的情况接收typescript 文件然后生成javascript 文件。然而我们不想混淆源文件和生成的输出。

我们将通过两个步骤解决这个问题：

* 首先，像这样组织我们的项目结构。我们将我们所有的源文件放到`src` 目录。

```
|-- package.json
|-- src
|   |- index.ts
|-- tsconfig.json
```

* 接下来，我们会告诉编译器源代码在哪以及输出该放哪。

```
// tsconfig.json

{
  "compilerOptions": {
    // ...
    "rootDir": "src",
    "outDir": "build"
    // ...
  },
}
```

非常棒！现在当我们运行构建脚本时编译器将会将生成的javascript 代码输出到`build` 文件夹。[TypeScript React Starter](https://github.com/Microsoft/TypeScript-React-Starter/blob/master/tsconfig.json) 提供了一个带有一套配置的`tsconfig.json` 文件让你上手。

通常，你不希望将生成的JavaScript 保留在源代码管理中，因此请确保将build 文件夹添加到 `.gitignore` 中。

#### File extensions

在React 中，你最有可能在`.js` 文件中编写你的组件。在TypeScript 中我们有两个文件扩展名：

`.ts` 是默认的文件扩展名，`.tsx` 是一个为包含`JSX` 代码使用的特殊扩展名。

#### Running TypeScript

如果你遵循了上述的说明，你应该能够在第一次就运行TypeScript。

```
yarn build
```

如果你使用npm，运行：

```
npm run build
```

你应该看到没有输出，这意味着它完全成功。

#### Type Definitions

为了能够显示来自其他包的错误和提示，编译器依赖于声明文件。 声明文件提供了关于库的所有类型信息。 这使我们能够在我们的项目中使用像那些在npm 中的JavaScript 库。

对于一个库来说，主要有两种方式获得声明：

**Bundled** - 该库捆绑了自己的声明文件。 这对我们来说很好，因为我们所要做的就是安装这个库，我们便可以马上使用它。 要检查一个库是否有bundled 类型，请在项目中查找`index.d.ts` 文件。 有些库会在`package.json` 文件的`typings` 或者`types` 域中配置它。

[DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) - DefinitelyTyped 是一个巨大的为不绑定声明文件的库的声明库。这些声明是由Microsoft 和开源贡献者提供的。 例如React 并不捆绑它自己的声明文件。 相反，我们可以从DefinitelyTyped 中获得。 为此，请在终端中输入此命令。

```
# yarn
yarn add --dev @types/react

# npm
npm i --save-dev @types/react
```

**Local Declarations** 有时你想使用的包不绑定声明，也不能在DefinitelyTyped 上使用。 在这种情况下，我们可以有一个本地声明文件。 为此，请在源目录的根目录中创建一个`declarations.d.ts` 文件。 一个简单的声明可能是这样的：

```
declare module 'querystring' {
  export function stringify(val: object): string
  export function parse(val: string): object
}
```

#### Using TypeScript with Create React App

[react-scripts-ts](https://www.npmjs.com/package/react-scripts-ts) 自动配置一个`create-react-app` 项目去支持TypeScript。你可以像这样使用它：

```
create-react-app my-app --scripts-version=react-scripts-ts
```

注意它是一个 **第三方库** 项目，不是Create React App 的一部分。

你也可以尝试[typescript-react-starter](https://github.com/Microsoft/TypeScript-React-Starter#typescript-react-starter)。

你已经准备好写代码了！我们建议查看以下资源来了解有关TypeScript 的更多信息：

* [TypeScript Documentation: Basic Types](https://www.typescriptlang.org/docs/handbook/basic-types.html)
* [TypeScript Documentation: Migrating from JavaScript](http://www.typescriptlang.org/docs/handbook/migrating-from-javascript.html)
* [TypeScript Documentation: React and Webpack](http://www.typescriptlang.org/docs/handbook/react-&-webpack.html)

### Reason

[Reason](https://reasonml.github.io/) 不是一种新的语言; 这是一个新的语法和工具链，由battle-tested 语言[OCaml](http://ocaml.org/) 提供支持。Reason 使OCaml 成为了面向 JavaScript 程序员的熟悉语法，而且迎合现有已知的NPM/Yarn 工作流。

Reason 是由Facebook 开发的，并且在其一些产品如Messenger 中使用。它仍然具有一定的实验性质，但它有由Facebook 维护的[dedicated React bindings](https://reasonml.github.io/reason-react/) 和一个[充满活力的社区](https://reasonml.github.io/community/)。

### Kotlin

[Kotlin](https://kotlinlang.org/) 是由JetBrains 开发的一门静态类型语言。其目标平台包括JVM，Android，LLVM 和[JavaScript](https://kotlinlang.org/docs/reference/js-overview.html)。

JetBrains 专门为React 社区开发和维护了几个工具：[React bindings](https://github.com/JetBrains/kotlin-wrappers) 以及[Create React Kotlin App](https://github.com/JetBrains/create-react-kotlin-app)。 后者可以帮助你开始使用Kotlin 构建React 应用程序，而不需要构建配置。

### Other Languages

注意，还有其他静态类型的语言可以编译成JavaScript，因此是React 兼容的。 例如，和[elmish-react](https://fable-elmish.github.io/react) 一起使用的[F#/Fable](http://fable.io/)。查看他们各自的网站以获取更多信息，并欢迎添加更多和与React 一起工作的静态类型的语言到这个页面！
