> 此文章是翻译[Installation](https://reactjs.org/docs/installation.html)这篇React（版本v16.2.0）官方文档。

## Installation

React 是灵活的并且可以在各种各样的项目中使用。你也可以使用它创建一个项目，但是你也可以逐渐引入它到一个已存在的代码库中而不需要进行重写。

以下是一些开始的方法：

* [Try React](https://reactjs.org/docs/installation.html#trying-out-react)
* [Create a New App](https://reactjs.org/docs/installation.html#creating-a-new-application)
* [Add React to an Existing App](https://reactjs.org/docs/installation.html#adding-react-to-an-existing-application)

### Trying Out React

如果你只是感兴趣尝试一下React，你可以使用CodePen。从[this Hello World example code](http://codepen.io/gaearon/pen/rrpgNB?editors=0010) 开始。你不用按照安装任何东西；你可以只修改代码就可以查看如果它工作。

如果你更喜欢使用你自己的文本编辑器，你也可以[download this HTML file](https://raw.githubusercontent.com/reactjs/reactjs.org/master/static/html/single-file-example.html)，编辑它，并且在你的浏览器中从本地文件系统打开它。它做一个慢的运行时代码转换，所以不在生产环境使用它。

如果你想将其应用到一个完整的应用程序，有两种流行的方式开始使用React：使用Create React App，或者将其添加到现有的应用程序。

### Creating a New Application

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

### Adding React to an Existing Application

你不需要重写你的应用去开始使用React。

我们推荐你添加React 到你的应用的一个小部分，就像一个独立的构建（wigdet），所以你可以看到，如果它为你的用例工作的很好。

虽然React 不需要一个构建管道也可以[被使用](https://reactjs.org/docs/react-without-es6.html)，我们推荐你设置它所以你可以更有成效。一个先进的构建管道通常包括：

* 一个包管理器（package manager），像[Yarn](https://yarnpkg.com/)或者[npm](https://www.npmjs.com/)。它让你利用一个第三方包的巨大生态系统，并且很容易安装和更新它们。
* 一个打包器（bundler），像[webpack](https://webpack.js.org/)或者[Browserify](http://browserify.org/)。它让你写模块化的代码并将它们一起打包到一个小的包中去优化加载时间。
* 一个编译器（complier）像[Babel](http://babeljs.io/)。它让你写现代JavaScript 代码却任然能够在旧浏览器中工作。

#### Installing React

>注意：

>一旦安装，我们强烈建议你设置[生产构建过程](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build)，以确保你在生产中使用最新版本的React。

我们推荐使用[Yarn](https://yarnpkg.com/) 或者[npm](https://www.npmjs.com/) 管理前端依赖。如果你是新接触包管理器，[Yarn documentation](https://yarnpkg.com/en/docs/getting-started) 是一个好地方开始入门。

使用Yarn 安装React，运行：

```
yarn init
yarn add react react-dom
```

使用npm 安装React，运行：

```
npm init
npm install -save react react-dom
```

Yarn 和npm 都从[npm registry](http://npmjs.com/)下载包。

#### Enabling ES6 and JSX

我们推荐使用[Babel](http://babeljs.io/) 编译React，让你在JavaScript 代码中使用ES6 和JSX。ES6 是现代JavaScript 特性的一个集，它使开发更简单，并且JSX 是JavaScript 语言的扩展所以同React 也很好的工作。

[Babel setup instructions](https://babeljs.io/docs/setup/)解释如何去在多个不同的构建环境中配置Babel。确保你安装[`babel-preset-react`](http://babeljs.io/docs/plugins/preset-react/#basic-setup-with-the-cli-) 和 [`babel-preset-env`](http://babeljs.io/docs/plugins/preset-env/)并且使它们在你的[`.babelrc` configuration](http://babeljs.io/docs/usage/babelrc/) 生效，并且你很好去做。

#### Hello World with ES6 and JSX

我们推荐使用像[webpack](https://webpack.js.org/) 或者[Browserify](http://browserify.org/) 打包器，所以你可以写模块化代码并且将它们一起打包到一个小的包中去优化加载时间。

最小的React 例子看上去像这样：

```
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
  <h1>Hello,world!</h1>,
  document.getElementById('root')
);
```

这块代码渲染到一个使用`root` id 的DOM 元素中，所以你需要在你的HTML 文件中某些地方有`<div id="root"></div>`。

相似的，你可以在任何由其他JavaScript UI 库编辑的现有应用中，将React 渲染进一个DOM 元素。

[了解更多关于在现有代码中集成React](https://reactjs.org/docs/integrating-with-other-libraries.html#integrating-with-other-view-libraries)

#### Development and Production Versions

默认地，React 包括很多有用的警告。这些警告在开发时是非常有用的。

**然而，它们使React 变得更大和更慢，所以你应该确保使用生产构建版本，当你部署应用时。**

学习[如何判断你的网站是否提供正确的React 版本](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build)，以及如何去更有效地配置生产构建过程：

* [使用Create React App 创建生产构建](https://reactjs.org/docs/optimizing-performance.html#create-react-app)
* [使用Signle-File Builds 创建生产构建](https://reactjs.org/docs/optimizing-performance.html#single-file-builds)
* [使用Brunch 创建生产构建](https://reactjs.org/docs/optimizing-performance.html#brunch)
* [使用Browserify 创建生产构建](https://reactjs.org/docs/optimizing-performance.html#browserify)
* [使用Rollup 创建生产构建](https://reactjs.org/docs/optimizing-performance.html#rollup)
* [使用Webpack 创建生产构建](https://reactjs.org/docs/optimizing-performance.html#webpack)

#### Using a CDN

如果你不想使用npm 去管理客户端包，`react` 和`react-dom` npm 包也提供单文件分发在`umd` 文件夹中，它托管在CDN 上：

```
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
```

上面的版本只打算为开发，并不适合生产。最小的和优化的生产版本React 在下面：

```
<script crossorigin src="https://unpkg.com/react@16/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js"></script>
```

为了加载一个`react` 和`react-dom`明确的版本，使用版本号替换`16`。

如果你是用Bowser，React 通过使用`react` 包。

**Why the crossorigin Attribute?**

如果你从一个CDN 提供React 服务，我们建议保持`crossorigin` 特性设置：

```
<script crossorigin src="..."></script>
```

我们还建议验证你正在使用的CDN 设置`Access-Control-Allow-Origin: *` HTTP报头：
![cdn-cors-header](img/cdn-cors-header.png)

这使得在React 16 版本之后有一个更好的[错误控制体验](https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html)。
