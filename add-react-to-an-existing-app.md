> 此文章是翻译[Add React to an Existing App](https://reactjs.org/docs/add-react-to-an-existing-app.html)这篇React（版本v16.2.0）官方文档。

## Add React to an Existing App

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
