---
title: 从零开始搭建React(1)
date: 2016-11-14
tags: javascript React Webpack
categories: 
- React
comments: true
---

学习React几乎绕不开Webpack，就像使用Java需要搭建环境、规划目录结构、写各种配置文件一样，这里我们也要搭建这样的一个脚手架。当然，这里只是我的简单总结，并不是什么最佳实践，只是一步步去理解这个搭建过程。  
目录结构开始只是简单的`myapp/src`，首先肯定是安装react、react-dom、webpack，webpack官网是安装在全局的。[参见官网](http://webpack.github.io/docs/tutorials/getting-started/#welcome)  
这时候就可以开始写webpack配置文件，在编写之前，可以安装eslint来规范代码，这里的配置文件我是写在`myapp/src/config/webpack.config.js`，最基本的样子如下（可以在官网找到各配置项表示，这里使用的是node API形式）：
```javascript
// webpack.config.js
var path = require('path')
var webpack = require('webpack')

var src = path.resolve(__dirname, '../../src')  // 源码目录
var build = path.resolve(__dirname, '../../dist')   // 编译目录

module.exports = {
  entry: {
    app: path.join(src, 'app.js')
  },
  output: {
    filename: '[name].js',
    path: build,
    publicPath: '/static/'
  },
  resolve: {
    extensions: ['', '.js']
  },
  module: {
    loaders: [{
      test: /\.js$/,
      include: src,
      loader: 'babel'
    }, {
      test: /\.scss$/,
      loaders: ['style', 'css', 'sass']
    }]
  }
}
```

接下来就是根据loaders安装各种webpack-loader，以后随着项目的需要，这里肯定还会添加更多的webpack-loader，安装方式就是`npm install --save-dev babel-loader style-loader css-loader sass-loader`。然后，创建`myapp/src/app.js`文件用于测试我们的环境。执行`webpack --config src/config/webpack.config.js`，会发现报错`can't find module 'babel-core'`，那么就按照提示，安装babel-core，一定要学会看提示/报错信息。  
再次执行`webpack --config src/config/webpack.config.js`(以下简称执行webpack)应该会生成`myapp/dist/app.js`文件，里面应该是只有一段webpackBootstrap以及一段空函数function(module, exports)。  
在`src/app.js`中编写代码用于环境测试：
```javascript
import React from 'react'
import ReactDOM from 'react-dom'

ReactDOM.render(
    <h1>Hello React</h1>,
    document.getElementById('app')
  )
```
再次执行`webpack --config src/config/webpack.config.js`，发现报错Unexpected token在<h1\>位置，显然是babel不能识别jsx格式，我们并没有配置babel的转换规则。创建`myapp/.babelrc`，一般的基础配置是，具体规则[参见官网](http://babeljs.io/docs/plugins/)。
```json
{
  "presets": ["es2015", "react", "stage-0"]
}
```
安装这些preset，再次执行webpack，生成编译后的文件。创建html文件：
```html
<!-- 我这里的路径是myapp/src/index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title></title>
</head>
<body>
  <div id="app"></div>
  <script type="text/javascript" src="../dist/app.js"></script>
</body>
</html>
```
用浏览器打开该文件，则能看到Hello React。  
然后测试sass文件怎么样，创建`myapp/src/app.scss`
```sass
h1 {
  background: red;
}
```
在`myapp/src/app.js`中引入该scss文件`import './app.scss'`  
执行webpack，可能会报错can't find module node-sass，再次安装此模块，执行webpack刷新页面，应该会看到Hello React背景色变成红色。

至此，一个最简单的webpack打包React的环境完成。虽然这个环境可以使用，但是没有好好利用webpack，也就没有提高一点我们的工作效率。