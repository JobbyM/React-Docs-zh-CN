> 此文章是翻译[How to Contribute](https://reactjs.org/docs/how-to-contribute.html)这篇React（版本v16.2.0）官方文档。

## How to Contribute

React 是Facebook 的第一个开源项目，它正在积极发展并且在迁移代码到[facebook.com](https://www.facebook.com/) 任何位置。我们仍然正在解决节点（kinkes）去是贡献这个项目尽可能的容易和透明，但是我们还没有达到。希望这篇文档能够使贡献清晰并且能够回答你可能遇到的一些问题。


### [Code of Conduct](https://code.facebook.com/codeofconduct)

Facebook 已经采纳Code of Conduct，我们希望项目参与者遵守。请阅读[全文（the full text）](https://code.facebook.com/codeofconduct)，这样你就可以了解什么行为会被或不会被允许。

### Open Development

在React 上的所有工作都直接发生在[GitHub](https://github.com/facebook/react) 上。包括核心团队成员和外部贡献者发送的pull request 都通过同样的审核过程。

### Branch Organization

我们将尽最大努力保持[`master` branch](https://github.com/facebook/react/tree/master) 良好状态，通过每一次测试都通过。但是为了快速移动（move），我们将使API 改变，你的应用程序可能不会兼容。我们推荐你使用[React 的最新稳定版本](https://reactjs.org/downloads.html)。

如果你发出一个pull request，请不要在`master` 分支上进行。我们分别为主版本保留稳定分支，但是我们不直接接受pull request。相反，我们从master 摘樱桃（cherry-pick）不破坏（non-breaking）改变到最新的稳定主版本。

### Semantic Versioning

React 遵循[语义化版本（sematic versioning）](http://semver.org/)。我们发布修订号（patch version）为问题修复，次版本号（minor version）为新特性，主版本号（major version）为任何突破性改变。当我们产生突破性改变，我们也引入废弃警告（deprecation warning）在次版本号，以至于我们的用户了解到升级改变以及提前迁移他们的代码。

我们使用标签为每一个pull request 打tag，标志改变是否应该进入下一个[patch](https://github.com/facebook/react/pulls?q=is:open+is:pr+label:semver-patch)、[minor](https://github.com/facebook/react/pulls?q=is:open+is:pr+label:semver-minor)、[major](https://github.com/facebook/react/pulls?q=is:open+is:pr+label:semver-major) 版本号中。我们每隔几周发布一个修订号，每隔几月发布一个次版本号，每年发布一两次主版本号。

每一次重大改变都在[changelog file](https://github.com/facebook/react/blob/master/CHANGELOG.md)记录。

### Bugs

#### Where to Find Know Issues

我们为我们的公共bug 使用[GitHub Issues](https://github.com/facebook/react/issues)。我们密切关注这块，并且当我们有一个内部解决进程时，将尝试解决它。在提交一个新的task 之前，请确保你的问题还没有存在过。

#### Reporting New Issues

使你的问题得到解决的最好方式是提供一个简化的测试例子。[JSFiddle tempalte](https://jsfiddle.net/reactjs/69z2wepo/) 是非常好的开始点。

#### Security Bugs

Facebook 有一个安全漏洞的安全泄漏的[奖金项目](https://www.facebook.com/whitehat/)。考虑到这一点，请不要提出公共问题；通过该页概述的过程。

### How to Get in Touch

* IRC: [#reactjs on freenode](https://webchat.freenode.net/?channels=reactjs)
* Discussion forum: [discuss.reactjs.org](https://discuss.reactjs.org/)

假使你需要React 帮助，这也有[an active community of React users on the Discord chat platform](http://www.reactiflux.com/)

### Proposing a Change

如果你打算更改公共的API，或者为实现任何重大的改变，我们推荐你[提交一个问题（filing an issue）](https://github.com/facebook/react/issues/new)。这使我们能够就你的建议达成协议，然后再付出巨大努力。

如果你只是修复一个bug，可以立即提交关于pull request ，但是我们仍推荐你提交一个问题，描述你修复的问题。这是有用的，假如我们不接收这个具体的修复（specific fix）但是想要跟踪这个问题。

### Your First Pull Request

这是你的第一个Pull Request？你能够从这免费的视频系列：[How to Contribute to an Open Source Project on GitHub](https://egghead.io/series/how-to-contribute-to-an-open-source-project-on-github)学习。

为了帮助你从实践中学习，并且让你熟悉我们的贡献构成，我们有一系列的[good first bugs](https://github.com/facebook/react/labels/good%20first%20bug)，它名包括相对容易去解决的bug。这是一个开始的伟大的地方。

如果你决定去修复一个问题，请确保检查这个评论线程，以防有人已经在修复这个问题。如果此刻没有人在工作，请留下一条评论，陈述你想要去工作，以至于其他人不会意外地重复你的努力。

如果有人提出了一个问题，但是超过两周没有被跟进，这是好的接管它，但是你应该仍然留一个评论。

### Sending a Pull Request

核心团队监控pull request。我们将审查你的pull request，要么合并它，请求改变它，要么解释关闭它。对于API 改变，我们可能需要去在Facebook.com 去修复我们内部的使用，这将导致一些延迟。我们将尽我们最大的努力去更新并且反馈整个过程。

**在提交一个pull request 之前，** 请确保以下操作完成：

1. 派生[the repository](https://github.com/facebook/react)，并且从`master` 中创建你的分支。
2. 在repository 根运行`yarn`。
3. 如果你已经修复了bug 或添加了代码，这些代码应该被测试，添加测试！
4. 确保测试组件通过（`yarn test`）。提示：`yarn test --watch TestName` 在开发时是有用的。
5. 在生产环境运行`yarn test-prod` 去测试。它和`yarn test` 支持相同的选项。
6. 如果你需要debugger，运行`yarn debug-test --watch TestName`，打开`chrome://inspect`，按“Inspect”。
7. 使用[prettier](https://github.com/prettier/prettier)（`yarn prettier`）格式化你的代码。
8. 确保你的代码lint（`yarn lint`）。提示：`yarn linc` 只检测改变的文件。
9. 运行[Flow](https://flowtype.org/) 类型检测（`npm run flow`）。
10. 如果你还没有，完成CLA。

### Contributor License Agreement(CLA)

为了接受你的pull request，我们需要你去提交CLA。你只需要去做一次，所以如果你已经为另一个Facebook 开源项目做过，你可以继续。如果你是第一次提交pull request，只要让我们知道你已经完成了CLA，我们可以再次确认（cross-check）你的GitHub 用户名

[Complete your CLA here.](https://code.facebook.com/cla)

### Contribution Prerequisites

* 你已经安装了v8.0.0+ 的[Node](https://nodejs.org/) ，以及v1.2.0+ 的[Yarn](https://yarnpkg.com/en/)。
* 你已经安装了`gcc`，或安装需要的编译器。我们`npm` 中的一些依赖可能需要一个编译步骤。在OS X 上，Xcode 命令行工具将包括这些。在Ubuntu 上，`apt-get install build-essential` 将会安装需要的包。类似的命令应该也在其它Linux distros 上工作。Windows 需要一些额外的步骤，查看[`node-gyp` installation instructions](https://github.com/nodejs/node-gyp#installation) 详细。
* 你熟悉`git`。

### Development Workflow

复制完React 之后，运行`yarn` 去获取它的依赖。然后，你可以运行下面几个命令：

* `yarn lint` 检测代码样式。
* `yarn linc` 同`yarn lint` 相似，但是更快，因为它只检测你的分支中文件不同。
* `yarn test` 运行完整的测试套件。
* `yarn test --watch` 运行一个交互测试观测者。
* `yarn test <pattern>` 在匹配的文件名运行测试。
* `yarn test-prod` 在生产环境中运行测试。它支持和`yarn test` 所有相同的选项。
* `yarn debug-test` 就像`yarn test` 但是使用debugger。打开`chrome://inspect`，按“Inspect”。
* `yarn flow` 运行[Flow](https://flowtype.org/) 类型检测。
* `yarn build` 创建一个所有包的`build` 文件夹。
* `yarn build core,dom --type=UMD` 创建只有React 和ReactDOM 的UMD 构建。

我们推荐运行`yarn test`（或者上面它的变种）去确保你不会引入任何回退，当你工作在你的改变上。然而，它可以被掌握去试验你的React 构建在一个真实的项目中。

首先，运行`yarn build` 。这将会生产预构建插件在`build` 文件夹，以及在`build/package` 中的准备的npm 包。

最简单的方式去试验你的改变是去运行`yarn build core,dom --type=UMD` 然后打开`fixtures/packaging/babel-standlalone/dev.html`。这文件已经使用来自`build`文件夹的`react.development.js` ，所以它能捡起你的变化。

如果你要在你的已经存在的React 项目中试验你的改变，你可以复制`build/dist/react.development.js`、`build/dist/react-dom.development.js` 或任何其它构建版本到你的应用中，并使用它们而不是使用稳定版本。如果你的项目使用来自npm 的React，你可以在依赖中删除`react` 和`react-dom`，使用`yarn link` 去指示它们在你本地的`build` 文件夹中：

```
cd ~/path_to_your_react_clone/build/packages/react
yarn link
cd ~/path_to_your_react_clone/build/packages/react-dom
yarn link
cd /path/to/your/project
yarn link react react-dom
```

每次在React 文件夹中运行`yarn build`，这个更新的版本将显示在你的项目的`node_modules`。你能够重新构建你的你的项目去试验的你的改变。

我们仍然需要你的pull request 包括任何新功能的单元测试。这种方式我们能够确保我们在未来不会破坏你的代码。

### Style Guide

我们使用称为[Prettier](https://prettier.io/)的自动的代码格式化。在任何代码改变之后运行`yarn prettier`。

我们linter 将会捕获存在与你的代码中大多数的问题。你可以通过简单的运行`yarn linc` 去查看你的代码样式的状态。

然而，仍然有一些linter 不能找到一些样式。如果你确认这些事，查看[Aribnb's Style Guide](https://github.com/airbnb/javascript) 将会给你一个正确的方向。

### Introductory Video

你可能有兴趣观看这个[短视频(26 mins)](https://www.youtube.com/watch?v=wUpPsEcGsg8)，介绍如何对React 做贡献。

**Video highlights:**

* [4:12](https://youtu.be/wUpPsEcGsg8?t=4m12s) - 本地构建和测试React
* [6:07](https://youtu.be/wUpPsEcGsg8?t=6m7s) - 创建和发送pull request
* [8:25](https://youtu.be/wUpPsEcGsg8?t=8m25s) - 组织代码
* [14:43](https://youtu.be/wUpPsEcGsg8?t=14m43s) - React npm registry
* [19:15](https://youtu.be/wUpPsEcGsg8?t=19m15s) - 添加新的React 特性

要了解第一次为React 做出贡献的感觉是什么，请看[这个有趣的ReactNYC 演讲](https://www.youtube.com/watch?v=GWCcZ6fnpn4)。


### Meeting Notes

React 小组每周召开一次会议，讨论React 的未来计划、优先项目的开发。你可以找到会议笔记在[a dedicated repository](https://github.com/reactjs/core-notes/)。

### License

通过给React 作贡献，你同意你的贡献将授权其在MIT 许可下。

### What Next?

阅读[下一章节](https://reactjs.org/contributing/codebase-overview.html) 去了解代码库是如何被组织的。
