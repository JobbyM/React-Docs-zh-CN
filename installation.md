

\#\# Getting Start



此文章是翻译\[Installation\]\(https://facebook.github.io/react/docs/installation.html\)这篇React（版本v15.4.0）官方文档。



\#\# Installation



React 是灵活的并且可以在各种各样的项目中使用。你也可以使用它创建一个项目，但是你也可以逐渐引入它到一个已存在的代码库中而不需要进行重写。



\#\# Trying Out React



如果你只是感兴趣尝试一下React，你可以使用CodePen。从\[this Hello World example code\]\(http://codepen.io/gaearon/pen/rrpgNB?editors=0010\) 开始。你不用按照安装任何东西；你可以只修改代码就可以查看如果它工作。



如果你更喜欢使用你自己的文本编辑器，你也可以\[download this HTML file\]\(https://facebook.github.io/react/downloads/single-file-example.html\)，编辑它，并且在你的浏览器中从本地文件系统打开它。它做一个慢的运行时代码转换，所以不在生产环境使用它。



\#\# Creating a Single Page Application



\[Create React App\]\(\) 是最好的方式去开始构建一个新的React 单页面应用。它设置你的开发环境，以至于你可以使用最新的JavaScript 特性，提供一个非常好的开发体验，并且优化你的应用在生产环境。

\`\`\`cmd

npm install -g create-react-app

create-react-app hello-world

cd hello-world

npm start

\`\`\`

Create React App不控制后端逻辑或数据库；它只创建一个前端构建管道（pipeline），所以你可以使用它同你想要的任何后端。它在底层使用\[webpack\]\(https://webpack.js.org/\)、\[Babel\]\(http://babeljs.io/\)和\[ESLint\]\(http://eslint.org/\)，但是为你配置它们。



\#\# Adding React to an Existing Application



你不需要重写你的应用去开始使用React。



我们推荐你添加React 到你的应用的一个小部分，就像一个独立的构建（wigdet），所以你可以看到，如果它为你的用例工作的很好。



虽然React 不需要一个构建管道也可以\[被使用\]\(https://facebook.github.io/react/docs/react-without-es6.html\)，我们推荐你设置它所以你可以更有成效。一个先进的构建管道通常包括：



\* 一个包管理器（package manager），像\[Yarn\]\(https://yarnpkg.com/\)或者\[npm\]\(https://www.npmjs.com/\)。它让你利用一个第三方包的巨大生态系统，并且很容易安装和更新它们。

\* 一个打包器（bundler），像\[webpack\]\(https://webpack.js.org/\)或者\[Browserify\]\(http://browserify.org/\)。它让你写模块化的代码并将它们一起打包到一个小的包中去优化加载时间。

\* 一个编译器（complier），像\[Babel\]\(http://babeljs.io/\)。它让你写现代JavaScript 代码却任然能够在旧浏览器中工作。



\#\#\# Installing React



我们推荐使用\[Yarn\]\(https://yarnpkg.com/\)或者\[npm\]\(https://www.npmjs.com/\)管理前端依赖。如果你是新接触包管理器，\[Yarn documentation\]\(https://yarnpkg.com/en/docs/getting-started\) 是一个好地方开始入门。



使用Yarn 安装React，运行：

\`\`\`cmd

yarn init

yarn add react react-dom

\`\`\`

使用npm 安装React，运行：

\`\`\`cmd

npm init

npm install -save react react-dom

\`\`\`

Yarn 和npm 都从\[npm registry\]\(http://npmjs.com/\)下载包。



\#\#\# Enabling ES6 and JSX



我们推荐使用\[Babel\]\(http://babeljs.io/\) 使用React，让你在你的JavaScript 代码中使用ES6 和JSX。ES6 是现代JavaScript 特性的一个集，它使开发更简单，并且JSX 是JavaScript 语言的扩展所以同React 也很好的工作。



\[Babel setup instructions\]\(https://babeljs.io/docs/setup/\)解释入工会去在多个不同的构建环境中配置Babel。确保你安装\[\`babel-preset-react\`\]\(http://babeljs.io/docs/plugins/preset-react/\#basic-setup-with-the-cli-\) 和 \[\`babel-preset-es2015\`\]\(http://babeljs.io/docs/plugins/preset-es2015/\#basic-setup-with-the-cli-\)并且使它们在你的\[\`.babelrc\` configuration\]\(http://babeljs.io/docs/usage/babelrc/\)，并且你很好去做。



\#\#\# Hello World with ES6 and JSX



我们推荐你使用像\[webpack\]\(https://webpack.js.org/\)或者\[Browserify\]\(http://browserify.org/\)打包器，所以你可以写模块化代码并且将它们一起打包到一个小的包中去优化加载时间。



最小的React 例子看上去像这样：

\`\`\`jsx

import React from 'react'

import ReactDOM from 'react-dom'



ReactDOM.render\(

     &lt;h1&gt;Hello,world!&lt;/h1&gt;,

     document.getElementById\('root'\)

\)

\`\`\`

这块代码使用\`root\` id 渲染到一个DOM element 中，所以你需要在你的HTML 文件中某些地方有\`&lt;div id="root"&gt;&lt;/div&gt;\`。



相似的，你可以渲染一个React component 到一个DOM element 中在你的使用任何其它JavaScript UI 库中的地方。



\#\#\# Development and Production Versions



默认地，React 包括很多有用的警告。这些警告在开发时是非常有用的。然而，它们是React 变得更大和更慢，所以你应该确保使用生产换件版本，当你部署应用时。



\#\#\#\# Create React App



如果你使用\[Create React App\]\(\)，\`npm run build\` 将会为你的应用创建一个优化的构建在\`build\` 文件夹中。



\#\#\#\# Webpack



包括\`DefinePlugin\` 和\`UglifyJsPlugin\` 到你的生产Webpack 配置作为在\[this guide\]\(\) 的描述



\#\#\#\# Browserify



使用\`NODE\_ENV\` 环境变量运行Browserify 并且使用\[UglifyJS\]\(\) 作为最新的构建步骤，以至于只能位于开发的代码被剥离出来。



\#\#\# Using a CDN



如果你不想使用npm 去管理客户端包，\`react\` 和\`react-dom\` npm 包也提供单文件分发在\`dist\` 文件夹中，它位于一个CDN 上：

\`\`\`jsx

&lt;script src="https://unpkg.com/react@15/dist/react.js"&gt;&lt;/script&gt;

&lt;script src="https://unpkg.com/react-dom@15/dist/react-dom.js"&gt;&lt;/script&gt;

\`\`\`

上面的版本只打算为开发，并不适合生产。最小的和优化的生产版本React 在下面：

\`\`\`jsx

&lt;script src="https://unpkg.com/react@15/dist/react.min.js"&gt;&lt;/script&gt;

&lt;script src="https://unpkg.com/react-dom@15/dist/react-dom.min.js"&gt;&lt;/script&gt;

\`\`\`

为了加载一个\`react\` 和\`react-dom\`明确的版本，使用版本号替换\`15\`。如果你是用Bowser，React 通过使用\`react\` 包。



\#\# 参考文档



1. \[Installation\]\(https://facebook.github.io/react/docs/installation.html\)



