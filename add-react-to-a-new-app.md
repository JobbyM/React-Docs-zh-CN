> 此文章是翻译[Add React to a New Application](https://reactjs.org/docs/add-react-to-a-new-app.html)这篇React（版本v16.2.0）官方文档。

## Add React to a New Application

开始一个新的React 项目最简单的方法是使用一个起动工具包。

>**注意：**

>本页描述了建立一个单页应用程序，为你提供舒适的开发工作流程所需的一切，包括linting、测试、生产优化等等。这些功能齐全的工具需要一些时间和磁盘空间来安装。

>如果你正在寻找一个轻量级的环境来尝试使用React，那么可以查看一下[Try React](https://reactjs.org/docs/try-react.html)页面。[一个HTML文件](https://raw.githubusercontent.com/reactjs/reactjs.org/master/static/html/single-file-example.html)就足以让你开始！

>最后，如果你没有构建单页应用程序，你可以[向现有的构建管道添加React](https://reactjs.org/docs/add-react-to-an-existing-app.html)，或者[从CDN中使用它](https://reactjs.org/docs/cdn-links.html)，并且[无需构建步骤](https://reactjs.org/docs/react-without-jsx.html)。

### Create React App

[Create React App](http://github.com/facebookincubator/create-react-app) 是最好的方式去开始构建一个新的React 单页面应用。它设置你的开发环境，以至于你可以使用最新的JavaScript 特性，提供一个非常好的开发体验，并且优化你的应用在生产环境。你需要在你的机器上npm >= 6。

```
npm install -g create-react-app
create-react-app my-app

cd my-app
npm start
```

如果你安装的npm 为5.2.0+，你可以使用[npx](https://www.npmjs.com/package/npx) 替代。

```
npx create-react-app my-app

cd my-app
npm start
```

Create React App 不控制后端逻辑或数据库；它只创建一个前端构建管道（pipeline），所以你可以使用它同你想要的任何后端。它在底层使用[Babel](http://babeljs.io/) 和[webpack](https://webpack.js.org/) 这样的构建工具，无需配置既可以工作。

当你准备部署到生产环境时，运行`npm run build` 将会在你的应用的`build` 文件夹下生成一个优化的构建。你可以在Create React App 的[README](https://github.com/facebookincubator/create-react-app#create-react-app-) 以及[User Guide](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#table-of-contents) 了解更多。

### Other Starter Kits

我们已经创建了[一个由我们正式推荐的第三方Starter 套件列表](https://reactjs.org/community/starter-kits.html)。

它们的关注点略有不同，但都准备生成、维护良好，并且不需要配置就能开始。

你还可以查看社区提供的[其他套件](https://reactjs.org/community/starter-kits.html#other-starter-kits)的列表。

### Advanced

如果你更喜欢手动配置项目，请参见下一节中的[安装React](https://reactjs.org/docs/add-react-to-an-existing-app.html#installing-react)。
