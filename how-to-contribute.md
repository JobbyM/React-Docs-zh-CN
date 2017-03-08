> 此文章是翻译[How to Contribute](https://facebook.github.io/react/contributing/how-to-contribute.html)这篇React（版本v15.4.0）官方文档。

## How to Contribute

React 是Facebook 的第一个开源项目，它正在积极发展并且在迁移代码到[facebook.com](https://www.facebook.com/) 任何位置。我们仍然正在解决节点（kinkes）去是贡献这个项目尽可能的容易和透明，但是我们还没有达到。希望这篇文档能够使贡献清晰并且能够回答你可能遇到的一些问题。


### [Code of Conduct](https://code.facebook.com/codeofconduct)

Facebook 已经采纳了我们希望项目参与者坚持的Code of Conduct。请阅读[全文（the full text）](https://code.facebook.com/codeofconduct)，这样你就可以了解什么行为将不会被允许。

### Open Development

在React 上的所有工作都直接发生在[GitHub](https://github.com/facebook/react) 上。包括核心团队成员和外部贡献者发送的pull request 都通过同样的审核过程。

### Branch Organization

我们将尽最大努力保持[`master` branch](https://github.com/facebook/react/tree/master) 良好状态，通过每一次测试都通过。但是为了快速移动（move），我们将使API 改变，你的应用程序可能不会兼容。我们推荐你使用[React 的最新稳定版本](https://facebook.github.io/react/downloads.html)。

如果你发出一个pull request，请不要在`master` 分支上。我们分别为主版本保留稳定分支，但是我们不直接接受pull request。相反，我们从master 摘樱桃（cherry-pick）不破坏（non-breaking）改变到最新的稳定主版本。

### Semantic Versioning

React 遵循[语义化版本（sematic versioning）](http://semver.org/)。我们发布修订号（patch version）为问题修复，次版本号（minor version）为新特性，主版本号（major version）为任何突破性改变。当我们产生突破性改变，我们也引入废弃警告（deprecation warning）在次版本号，以至于我们的用户了解到升级改变以及提前迁移他们的代码。

我们使用标签为每一个pull request 打tag，标志改变是否应该进入下一个[patch](https://github.com/facebook/react/pulls?q=is%3Aopen+is%3Apr+label%3Asemver-patch)、[minor](https://github.com/facebook/react/pulls?q=is%3Aopen+is%3Apr+label%3Asemver-minor)、[major](https://github.com/facebook/react/pulls?q=is%3Aopen+is%3Apr+label%3Asemver-major) 版本号中。我们每隔几周发布一个修订号，每隔几月发布一个次版本号，每年发布一两次主版本号。

每一次重大改变都在[changelog file](https://github.com/facebook/react/blob/master/CHANGELOG.md)记录。

### Bugs

#### Where to Find Know Issues

我们为我们的公共bug 使用[GitHub Issues](https://github.com/facebook/react/issues)。我们密切关注这块，并且当我们有一个内部解决进程时，将尝试解决它。在提交一个新的task 之前，请确保你的问题还没有存在过。

#### Reporting New Issues

使你的问题得到解决的最好方式是提供一个简介的测试例子。[JSFiddle tempalte](https://jsfiddle.net/reactjs/69z2wepo/) 是非常好的开始点。

#### Security Bugs

Facebook 有一个安全漏洞的安全泄漏的奖金项目。考虑到这一点，请不要，请不要写到公共问题中；通过该页概述的过程。

### How to Get in Touch

* IRC: [#reactjs on freenode](https://webchat.freenode.net/?channels=reactjs)
* Discussion forum: [discuss.reactjs.org](https://discuss.reactjs.org/)

假使你需要React 帮助，这也有[an active community of React users on the Discord chat platform](http://www.reactiflux.com/)

### Proposing a Change

如果你打算更改公共的API，或者为实现任何重大的改变，我们推荐你[提交一个问题（filing an issue）](https://github.com/facebook/react/issues/new)。在你投入大量努力之前，先让我们对你的建议达成一致。

如果你只是修复一个bug，提交关于pull request 是否正确的方式，但是我们仍推荐你提交一个问题，描述你修复的问题。这是有用的，假如我们不接收这个具体的修复（specific fix）但是想要跟踪这个问题。

### Your First Pull Request

工作在你的第一个Pull Request？你能够从这免费的视频系列：[How to Contribute to an Open Source Project on GitHub](https://egghead.io/series/how-to-contribute-to-an-open-source-project-on-github)学习。

为了帮助你从实践中学习，并且让你熟悉我们的贡献构成，我们有一系列的[good first bugs](https://github.com/facebook/react/labels/good%20first%20bug)，它名包括相对容易去解决的bug。这是一个开始的伟大的地方。

如果你决定去修复一个问题，请确保检查这个评论线程，以防有人已经在修复这个问题。如果此刻没有人在工作，请留下一条评论，陈述你想要去工作，以至于其他人不会意外地重复你的努力。

如果有人提出了一个问题，但是超过两周没有被跟进，这是好的接管它，但是你应该仍然留一个评论。

### Sending a Pull Request

核心团队监控pull request。我们将审查你的pull request，要么合并它，请求改变它，要么解释关闭它。对于API 改变，我们可能需要去在Facebook.com 去修复我们内部的使用，这将导致一些延迟。我们将尽我们最大的努力去更新并且反馈整个过程。

**在提交一个pull request 之前，** 请确保以下操作完成：

1. 派生[the repository](https://github.com/facebook/react)，并且从`master` 中创建你的分支。
2. 如果你已经添加了代码，这些代码应该被测试，添加测试！
3. 如果你已经改变了APIs，更新这个文档。
4. 确保测试套件通过（`npm test`）。
5. 确保你的代码lints（`npm run lint`）。
6. 运行[Flow](https://flowtype.org/) 类型检测（`npm run flow`）。
7. 如果你还没有，完成CLA。

### Contributor License Agreement(CLA)

为了接受你的pull request，我们需要你去提交CLA。你只需要去做一次，所以如果你已经为另一个Facebook 开源项目做过，你可以继续。如果你是第一次提交pull request，只要让我们知道你已经完成了CLA，我们可以再次确认（cross-check）你的GitHub 用户名

[Complete your CLA here.](https://code.facebook.com/cla)

### Contribution Prerequisites

* 你已经安装了v4.0.0+ 的`node` ，以及v2.0.0+ 的`npm`。
* 你已经安装了`gcc`，或安装需要的编译器。我们`npm` 中的一些依赖可能需要一个编译步骤。在OS X 上，Xcode 命令行工具将包括这限额。在Ubuntu 上，`apt-get install build-essential` 将会安装需要的包。类似的命令应该也在其它Linux distros 上工作。Windows 需要一些额外的步骤，查看[`node-gyp` installation instructions](https://github.com/nodejs/node-gyp#installation) 详细。
* 你熟悉`npm` 并且知道当安装全局包时，是否需要使用`sudo`。
* 你熟悉`git`。

### Development Workflow

复制完React 之后，运行`npm install` 去获取它的依赖。然后，你可以运行下面几个命令：

* `npm run lint` 检测代码样式。
* `npm test` 运行完整的测试套件。
* `npm test -- --watch` 运行一个交互测试观测者。
* `npm test <pattern>` 在匹配的文件名运行测试。
* `npm run flow` 运行[Flow](https://flowtype.org/) 类型检测。
* `npm run build` 创建一个所有包的`build` 文件夹。

我们推荐运行`npm test`（获取它的变量上面）去确保你不会引入任何回退，当你工作在你的改变上。然而，它可以被掌握去试验你的React 构建在一个真实的项目中。

首先，运行`npm run build` 。这将会生产预构建插件在`build` 文件夹，以及在`build/package` 中的准备的npm 包。

最简单的方式去试验你的改变是去打开并修改`examples/basic/index.html` 。这文件已经使用来自`build `文件夹的`react.js` ，所以它能捡起你的变化。请确保去回滚在`examples` 中任何意外的变化，在发送一个pull request 之前。

如果你要在你的已经存在的React 项目中试验你的改变，你可以复制`build/react.js`、`build/react-dom.js` 或任何其它构建产品到你的应用中，并使用它们而不是使用稳定版本。如果你的项目使用来自npm 的React，你可以在依赖中删除`react` 和`react-dom`，使用`npm link` 去指示它们在你本地的`build` 文件夹中：
```jsx
cd your_project
npm link ~/path_to_your_react_clone/build/packages/react
npm link ~/path_to_your_react_clone/build/packages/react-dom
```
每次在React 文件夹中运行`npm run build`，这个更新的版本将显示在你的项目的`node_modules`。你能够重新构建你的你的项目去试验的你的改变。

我们仍然需要你的pull request 包括任何新功能的单元测试。这种方式我们能够确保我们在未来不会破坏你的代码。

### Style Guide

我们linter 将会捕获存在与你的代码中大多数的样式问题。你可以通过简单的运行`npm run lint` 去查看你的代码样式的状态。

然而，仍然有一些linter 不能找到一些样式。如果你不缺席呢这些事，查看[Aribnb's Style Guide](https://github.com/airbnb/javascript) 将会给你一个正确的方向。

### Code Conventions

* 使用分号 `;`
* 最后一个逗号 `,`
* 2 个空格代表一个缩进（不是tab）
* 使用`'` 而不是`"`
* `'use strict';`
* 每行80 个字符（**除了文档**）
*  书写“有魅力的（attractive）”代码
* 不要使用`setTimeout` 和`setInterval` 可选的参数

### Introductory Video

你可以对观看结合扫如何对React 做贡献的[短视频(26 mins)](https://www.youtube.com/watch?v=wUpPsEcGsg8)感兴趣。

### Meeting Notes

React 小组每周召开一次会议，讨论React 的未来计划、优先项目的开发。你可以找到会议笔记在[a dedicated repository](https://github.com/reactjs/core-notes/)。

### License

通过给React 作贡献，你同意你的贡献将授权其在BSD 许可下。

### What Next?

阅读[下一章节](https://facebook.github.io/react/contributing/codebase-overview.html) 去了解代码库是如何被组织的。
