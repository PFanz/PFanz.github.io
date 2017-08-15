---
title: CSS后处理PostCSS
date: 2017-08-14
tags: [CSS]
categories: 
- CSS
comments: true
---

Less、Sass是比较常用的CSS预处理器，使用预处理器可以帮助我们实现在CSS中，像编程一样使用变量、条件控制、循环等等功能，我们需要做的就是学习一套Less或者Sass的语法，并用该语法来编写样式表，最后通过Less或Sass将其转换为CSS样式表。  
PostCSS则不同，PostCSS对于使用者不需要学习其他语法，只需要正常编写CSS，最后PostCSS会根据配置功能对该CSS样式表进行修改。比如，最常用的AutoPrefixer就是通过PostCSS实现的，我们在使用的时候，不需要写额外的内容，只需要编写一些配置使用AutoPrefixer即可。

在PostCSS的官网上，我们可以看到对PostCSS的描述，A tool for transforming CSS with JavaScript。PostCSS只是一个用于提供使用JavaScript去转换CSS的工具，本身并没有实现对CSS的任何修改。PostCSS可以分析CSS文件，将所有的CSS规则转换成AST（抽象语法树），并提供了一系列的API对其可以进行遍历、修改，最终重新生成CSS文件。

首先根据官网的教程，我们可以发现一个简单的插件架子是这样的。
```JavaScript
const postcss = require('postcss')

module.exports = postcss.plugin('myplugin', function myplugin(options) {
  return function(css) {
    options = options || {}
    // 这里是对CSS的处理
  }
})
```
首先是将整个文件转换成了AST对象（可以通过[这里](http://astexplorer.net/#/2uBU1BLuJ1)查看抽象语法树结构），存储在css变量中。这个对象（Root对象）是这样子的，在子节点中包含所有规则
```JavaScript
Root {
  raws: { semicolon: false, after: '\n\n\n' },    // 是否包含分号，结尾内容
  type: 'root',                                   // 节点类型
  nodes: [
    Rule {                                  // 子节点
      raws: [Object],
      type: 'rule',
      nodes: [Object],
      parent: [Circular],
      source: [Object],
      selector: 'body'
    }
  ],
  source: {
    input: Input {
      css: '\nbody {\n  margin: 0;\n}\n\n\n',
      file: '/Users/huanqiu/Desktop/Fanz/postCssDemo/index.css'
    },
    start: { line: 1, column: 1 }
  }
}
```
之后，通过Root的walkRules可以遍历每一个子节点，也就是Rule对象，这里就是一条规则，在子节点中包含所有声明
```JavaScript
Rule {
  raws: { before: '\n', between: ' ', semicolon: true, after: '\n' },
  type: 'rule',
  nodes: [Declaration {
    raws: [Object],
    type: 'decl',
    parent: [Circular],
    source: [Object],
    prop: 'margin',
    value: '0'
  }],
  parent: Root {
    raws: { semicolon: false, after: '\n\n\n' },
    type: 'root',
    nodes: [
      [Circular]
    ],
    source: { input: [Object], start: [Object] },
    lastEach: 1,
    indexes: { '1': 0 }
  },
  source: {
    start: { line: 2, column: 1 },
    input: Input {
      css: '\nbody {\n  margin: 0;\n}\n\n\n',
      file: '/Users/huanqiu/Desktop/Fanz/postCssDemo/index.css'
    },
    end: { line: 4, column: 1 }
  },
  selector: 'body'
}
```
再之后，可以通过Rule对象的walkDecls拿到每一条声明。（通过Root直接使用walkDecls也可以直接拿到声明）
```JavaScript
Declaration {
  raws: { before: '\n  ', between: ': ' },
  type: 'decl',
  parent: 
    Rule {
     raws: { before: '\n', between: ' ', semicolon: true, after: '\n' },
     type: 'rule',
     nodes: [ [Circular] ],
     parent: 
      Root {
        raws: [Object],
        type: 'root',
        nodes: [Object],
        source: [Object],
        lastEach: 1,
        indexes: [Object] },
     source: { start: [Object], input: [Object], end: [Object] },
     selector: 'body',
     lastEach: 1,
     indexes: { '1': 0 } 
    },
  source: { 
    start: { line: 3, column: 3 },
    input: 
      Input {
        css: '\nbody {\n  margin: 0;\n}\n\n\n',
        file: '/Users/huanqiu/Desktop/Fanz/postCssDemo/index.css' },
     end: { line: 3, column: 12 } },
  prop: 'margin',
  value: '0' 
}
```
在任何对象上，我们都可以通过PostCSS提供的API对CSS进行修改。比如实现一个简单的为transform添加-webkit-前缀功能，在walkDecls方法中传入下面方法作为参数
```JavaScript
function (decl, i) {
  if (decl.prop === 'transform') {
    decl.before('-webkit-transform: ' + decl.value)
  }
}
```
更多API可以参考[这里](http://api.postcss.org)

在CSS中，有注释、CSS规则、规则中的声明以及媒体查询规则，分别对应PostCSS中的Comment、Rule、Declaration、AtRule对象。除此之外，还有Root对象可以用来表示整个CSS文件；Node对象是所有节点类型对象的祖先对象；Container对象是Root、Rule、AtRule的祖先对象；Result对象是PostCSS转换结果的对象，包含css、map等内容；Warning、CssSyntaxError等等。
