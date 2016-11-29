---
title: 从零开始搭建React(2)
date: 2016-11-14
tags: javascript React Webpack
categories: 
- React
comments: true
---

提高开发效率，我们发现前面用的最多的就是`webpack --config ***`，然后去刷新页面。解决此问题，可以使用[react-hot-loader](https://github.com/gaearon/react-hot-loader)，这应该也是我们使用github的原因，在这里我们可以找到解决各种问题的办法。  
查看其中使用方法（看不懂英文就用翻译一句一句看呗，其实我也是这么看过来的），会发现这一句These steps are covered by [the walkthrough](http://gaearon.github.io/react-hot-loader/getstarted/)（这里应该是1版本的写法）。  
根据这里的步骤，先是安装react-hot-loader，然后是server.js，提供有事例：
```javascript
// 我的路径 myapp/src/config/server.js
var path = require('path')
var webpack = require('webpack')
var express = require('express')
var config = require('./webpack.config')

var app = express()
var compiler = webpack(config)

app.use(require('webpack-dev-middleware')(compiler, {
  publicPath: config.output.publicPath
}))

app.use(require('webpack-hot-middleware')(compiler))

app.get('*', function (req, res) {
  res.sendFile(path.join(__dirname, '../index.html'))
})

app.listen(3000, function (err) {
  if (err) {
    return console.error(err)
  }

  console.log('Listening at http://localhost:3000/')
})
```
需要安装express，另外需要修改`res.sendFile(path.join(__dirname, '../index.html'))`这里的路径，还有端口。  
接着修改webpack的配置文件，在入口配置项前面添加，
```javascript
entry: {
  app: [
  'webpack-dev-server/client?http://0.0.0.0:3000', // WebpackDevServer host and port
  'webpack/hot/only-dev-server', // "only" prevents reload on syntax errors
  path.join(src, 'app.js')
  ]
}
```
这个应该是1版本的写法，而我这里安装的是3版本，导致页面会多次刷新。下面是3版本，具体参看[事例](https://github.com/gaearon/react-hot-boilerplate/tree/next)
```javascript
entry: {
    app: [
      'react-hot-loader/patch',
      'webpack-hot-middleware/client',
      path.join(src, 'app.js')
    ]
  },
```
接着是在oader中添加react-hot，按照官网是将react-hot添加在babel前面即可
```javascript
loaders: [{
  test: /\.js$/,
  include: src,
  loaders: ['react-hot', 'babel']
}]
```
但是在我这里报错了，报错提示还算明显，大致就是将react-hot-loader/babel添加到babel的plugins中，于是修改`myapp/.babelrc`解决。同样是1和3的区别
```json
{
  "presets": ["es2015", "react", "stage-0"],
  "plugins": ["react-hot-loader/babel"]
}
```
最后一步，是webpack中添加plugins
```javascript
plugins: [
  new webpack.HotModuleReplacementPlugin()
]
```
1版本到这里就结束了，3版本是这样的
```javascript
// myapp/src/app.js
import React from 'react'
import ReactDOM from 'react-dom'
import { AppContainer } from 'react-hot-loader'
import Hello from './Hello'
import './app.scss'

ReactDOM.render(
    <AppContainer>
      <Hello />
    </AppContainer>,
    document.getElementById('app')
  )

if (module.hot) {
  module.hot.accept('./Hello', () => {
    // If you use Webpack 2 in ES modules mode, you can
    // use <App /> here rather than require() a <NextApp />.
    const NextApp = require('./Hello').default
    ReactDOM.render(
      <AppContainer>
         <NextApp />
      </AppContainer>,
      document.getElementById('app')
    )
  })
}
```
```javascript
// myapp/src/Hello.js
import React from 'react'
export default function Layout () {
  return (
    <div>
      <h1>Hello, world!!</h1>
    </div>
  )
}
```
这里有个小坑，改变app.js文件中的内容并不会刷新，需要手动刷新页面。

启动就是`node src/config/server.js`，可以将其写入到package.json文件中`"script": {"start": "node src/config/server.js"}`

最后遇到一个小坑，在HTML文件引用入口文件中，地址应该是webpack配置中的`output.publicPath/[name].js`，如`<script src="/static/app.js"></script>`。引用不到，或者路径错误会将html文件作为js文件引入，报错。  
至此，在之后的编写中就不需要一遍又一遍的`webpack --config ****`

------------------
11.18补充

分离打包：  
在入口文件中添加要分离的模块：(这里将react和react-dom打包到vendor中)
```javascript
entry: {
    app: [
      'react-hot-loader/patch',
      'webpack-hot-middleware/client',
      path.join(src, 'index.js')
    ],
+    // 打包分离
+    vendor: [
+      'react',
+      'react-dom'
+    ]
  },
```
增加CommonsChunkPlugin插件：
```javascript
  plugins: [
+    // 分离打包
+    new webpack.optimize.CommonsChunkPlugin({
+      names: ['vendor']
+    }),
    // 热加载
    new webpack.HotModuleReplacementPlugin()
  ]
```
分离打包后，两个app.js和vendor.js都需要引入。