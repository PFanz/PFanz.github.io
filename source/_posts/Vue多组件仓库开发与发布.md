---
title: Vue多组件仓库开发与发布
date: 2019-02-25
tags: [JavaScript, Vue]
categories:
- JavaScript
comments: true
---

在开发组件时，我们可能会期望一类组件放在同一个代码仓库下，就像`element`那样，我们可以使用`element`提供的脚手架，也可以使用`vue cli 3`创建一个更‘新’的项目。

<!-- more -->

## 项目创建

通过`vue cli 3`创建项目，创建文件夹`packages`用于存放组件。

#### 单个组件目录

在`packages`下就是每一个组件，每个组件和单独项目一样，会有`package.json`、`README.md`、`src`、`dist`等文件及目录。

#### 如何演示/调试组件

在组件开发过称中，我们需要对组件进行展示，所以创建了`examples`文件夹，用于存放每个组件示例。  
通过一个列表展示出所有的组件，点击选择当前开发的组件，进入对应的`example`。  
路由的根就是一个导航列表，然后每个组件对应一个路由，通过一个配置文件的`components.js`来生成这个路由。

```javascript
// 路由
import Navigation from "./Navigation";
import components from "./components";

let routes = components.map(component => ({
  path: `/${component.name}`,
  component: () => import(`../examples/${component.name}`)
}));

routes.unshift({
  path: "",
  component: Navigation
});

export default routes;
```

## 自动化脚本

#### 创建/编译/发布

创建新的组件，需要修改`components.js`配置文件，在`examples`和`packages`下创建对应目录。  
编译/发布组件，因为仓库下会有多个组件，如果一次发布多个，就需要进入每个文件夹下执行命令。  
上面过程实现自动化，有很多种方式，比如可以通过`npm run <script>`，可以直接通过`node`命令等。这里我参考`element`，采用了Makefile。

创建`script`文件夹，其中包括创建脚本`new.js`和构建脚本`build.js`。  

#### 创建脚本

创建脚本主要就是目录的创建与文件的写入，其中可能需要注意的可能就是格式问题。  
一种方式是在\`\`之间，按照规范格式去完成写入内容，这样做比较麻烦，而且可能面临格式化要求修改问题。  
另一种方式是在脚本中引入`eslint`，脚本中的`eslint.CLIEngine`可以根据配置文件(比如`.eslintrc.js`)格式化文件。需要注意的是需要比命令行中配置需要多添加`fix: true`配置, 如下
```javascript
const CLIEngine = eslint.CLIEngine;
const cli = new CLIEngine({ ...require("../.eslintrc.js"), fix: true });
```
`eslint`在脚本中的使用方法，更具体的可以参考[eslint文档中Node.js API部分](https://eslint.org/docs/developer-guide/nodejs-api)。

```javascript
// scripts/new.js部分
...

components.push({
  label: newName,
  name: newName
})

const updateConfig = function(path, components) {
  writeFile(path, `module.exports = ${JSON.stringify(components)}`).then(() => {
    console.log("完成components.js")
    // 格式化
    CLIEngine.outputFixes(cli.executeOnFiles([configPath]))
  })
}

const createPackages = function(componentName) {
  try {
    const dir = path.resolve(__dirname, `../packages/${componentName}/`)
    // 创建文件夹
    if (!fs.existsSync(dir)) {
      fs.mkdirSync(dir)
      console.log(`完成创建packages/${componentName}文件夹`)
    }
    // 写入README
    if (!fs.existsSync(`${dir}/README.md`)) {
      writeFile(
        `${dir}/README.md`,
        `## ${componentName}
  
  ### 使用说明
            `
      ).then(() => {
        console.log("完成创建README")
      })
    }
    // 写入package.json
    if (!fs.existsSync(`${dir}/package.json`)) {
      writeFile(
        `${dir}/package.json`,
        `{
  "name": "@hy/${componentName}",
  "version": "1.0.0",
  "description": "${componentName}",
  "main": "./dist/hy-${componentName}.umd.min.js",
  "keywords": [
    "${componentName}",
    "vue"
  ],
  "author": "",
  "license": "ISC"
}
        `
      ).then(() => {
        console.log("完成创建package.json")
      })
    }
    // 创建index.js
    if (!fs.existsSync(`${dir}/index.js`)) {
      writeFile(`${dir}/index.js`, `export {}`).then(() => {
        console.log("完成创建index.js")
        CLIEngine.outputFixes(cli.executeOnFiles([`${dir}/index.js`]))
      })
    }
  } catch (err) {
    console.error(err)
  }
}

const createExample = function(componentName) {
  try {
    const dir = path.resolve(__dirname, `../examples/${componentName}/`)
    // 创建文件夹
    if (!fs.existsSync(dir)) {
      fs.mkdirSync(dir)
      console.log(`完成创建examples/${componentName}文件夹`)
    }
    // 写入index.vue
    if (!fs.existsSync(`${dir}/index.vue`)) {
      writeFile(
        `${dir}/index.vue`,
        `<template>

</template>

<script>
import { } from '../../packages/${componentName}/index'

export default {
  components: {}
}

</script>
      `
      ).then(() => {
        console.log(`完成创建examples/${componentName}/index.vue文件`)
        // 格式化index.vue
        CLIEngine.outputFixes(cli.executeOnFiles([`${dir}/index.vue`]))
      })
    }
  } catch (err) {
    console.error(err)
  }
}

...
```

#### 构建脚本

```javascript
// build.js
...

async function build() {
  for (let i = 0, len = components.length; i < len; i++) {
    const name = components[i].name
    await buildService.run(
      "build",
      {
        _: ["build", `${root}/packages/${name}/src/index.js`],
        target: "lib",
        name: `hy-${name}`,
        dest: `${root}/packages/${name}/dist`,
        // 生成格式: umd格式会同时成功demo.html commonjs,umd,umd-min
        formats: "commonjs,umd-min"
        // clean: false
      },
      ["--target=all", `./packages/${name}/src/index.js`]
    )
  }
}

...
```

#### Lerna

`lerna`是一个多包仓库管理的工具，可以帮助创建、管理、发布多包仓库中的包。  
关于`lerna`我也没有太深入得使用，只是用到了发布。首先在项目下执行`init`初始化了项目，在每次`commit`之后，可以执行`publish`。`lerna`会对应代码库打tag，并发布到npm仓库。

#### 项目版本问题

0.0.1为不规范版本号，最小应该从1.0.0开始。`npm publish`无法发布，但是`lerna publish`可以发布。  
导致结果安装为固定版本号，而不是以`^`开头的版本号范围。`outdate`可以检测到有更新，无法通过`update`升级。

## 组件开发

组件开发主要是在`packages/<component name>/src`目录下进行，在`example/<component name>/`目录下可以引入该组件`src`下的源文件，用一些数据来进行开发测试。组件开发和项目中的组件开发基本相同。  
作为组件库中的组件，需要更多的考虑其通用性和易用性。不能为了通用而加入很多的属性，而使其失去易用性；同样也不能为了易用，而使其过于简单，使用范围过于局限。  
对于每一个属性、每个抛出去的方法，都需要认真考虑其必要性。

唯一不同的地方可能需要注意的是导出的方式。  
一种是直接导出组件，这种形式在使用时需要引入，并且在`components`中声明，也就是局部注册。  
另一种是添加`install`方法后导出。这种形式需要调用`vue.use`方法，相当于全局注册。
