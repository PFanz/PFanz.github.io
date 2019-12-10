---
title: Vue多组件仓库搭建
date: 2019-07-10
tags: [JavaScript, Vue]
categories:
- JavaScript
comments: true
---

组件化是目前前端最为流行的代码复用方式，无论是React还是Vue也都是以组件为单元进行开发的。  
在我们的工作过程中，随着业务的增长，积累下来的组件也越来越多。这时候，如何高效的组织我们的组件就成了一个需要解决的问题。  

## 代码存放

在项目创建之前，我们比较了现在主流的两种代码仓库存储方式——`monorepo`和`multirepo`。  

`multirepo`中，我们以前端打包工具`rollup`为例，完整的项目应该是包括`rollup`主仓库，以及`rollup-plugin-alias`、`rollup-plugin-buble`、`rollup-starter-lib`等很多相关仓库。  

`multirepo`会有以下特点：  
* 每个仓库中代码都是完整的项目，拥有完全的控制权和自主管理能力。
* 各个仓库可以分配给不同的团队，团队可以使用自身擅长的工具、工作流，甚至编程语言等。
* 当依赖发生变化或需要修改依赖时，需要在不同的仓库来回切换更改代码。
* 多个仓库会增加一定的沟通成本。
* 用户在使用过程中遇到问题，常常会直接在主仓库下提问，而这些问题很多是属于其他模块的，应该在其他仓库中，导致`issue`混乱。

`monorepo`中，以`JavaScript`编译工具`babel`为例，项目中在`packages`文件夹下有`babel-cli`、`babel-core`、`babel-helper-*`、`babel-plugin-*`等模块。  

`monorepo`会有以为特点：  
* 所有代码存放在同一个代码仓库。
* 仓库变大给版本控制带来挑战，`Git`社区建议使用更多更小的代码仓库，`Git`不太适合管理体积太大的代码仓库。
* 对构建工具要求较高，需要构建工具可以构建`packages`下的所有模块。
* 每个开发者都可以看到所有代码。
* 跨项目更改代码要容易一些。

比较过后，我们选择了按`monorepo`的方式来存放我们的组件。主要有两方面的原因，一方面是这些组件存在依赖的情况，并且可能变动会比较频繁，另一方面是目前我们统一了开发库为`vue`，构建工具本身就是统一的。

## 项目创建

项目的创建主要使用了`@vue/cli`，它是官方提供的基于Vue.js进行快速开发的完整的脚手架、工具链系统。 

创建时，cli的交互式配置省去了对`webpack`以及各种`loader`的繁杂编写。  
开发过程中，cli基于`webpack-dev-server`提供了支持模块热重载的开发服务器。  
完成后，cli的构建功能提供了“应用、库、Web组件”等多种构建目标选择。  
这些可以很好的满足我们组件库的需求。

#### 组件的存放

在使用`@vue/cli`搭建起项目脚手架后，参照`babel`、`Element`等`monorepo`，在项目根目录下创建`packages`文件夹。该文件夹下每个组件对应一个目录，在目录下存放`package.json`、`README.md`、`CHANGELOG.md`等文件，以及`src`、`dist`、`node_module`等目录。

```bash
├── packages
│   ├── error-page-vue
│   │   └── ...
│   ├── feed
│   │   ├── CHANGELOG.md
│   │   ├── README.md
│   │   ├── dist
│   │   ├── node_modules
│   │   ├── package-lock.json
│   │   ├── package.json
│   │   └── src
│   ├── feed-edit
│   │   └──  ...
│   └──  ...
└──  ...
```

因为该项目中存放的都是Vue组件，我们可以把每个组件都需要的Vue相关的依赖以及构建相关的依赖（比如`vue`、`babel`、`sass`等依赖）统一放在项目的根目录下，组件目录下只包含了组件业务的依赖。这样，我们的组件依赖就会纯粹很多。

#### 演示/调试组件

组件包括了对功能和样式的复用，所以在组件开发/调试过程中，需要对组件进行展示。  
我们在项目根目录下创建了`examples`文件夹
，文件夹下对应每个组件创建vue文件作为示例。  
为了更方便的查看对应的示例，我们维护了一个配置文件`components.js`，该配置文件用于项目中示例路由以及导航列表的创建等。

```javascript
// routes.js

// Navigation.vue是导航组件
import Navigation from "./Navigation";
// components.js是维护组件的数组
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

## 创建/构建组件

根据上面创建的目录结构，在创建新的组件时，我们需要修改`components.js`配置文件，在`examples`和`packages`目录下分别创建对应的组件。  
在完成组件的开发后，需要构建并发布组件，如果同时修改了多个组件（比如有依赖的两个组件），最好是可以同时构建多个组件并发布。  

我们可以通过脚本来实现诸如此类便捷化的需求，在项目根目录下创建`scripts`文件夹用来存放这些脚本。目前我们项目中包括创建组件脚本`new.js`、构建脚本`build.js`、删除脚本`delete.js`等。  

#### 创建脚本

根据需求，创建脚本就可以分为三部分。

```javascript
// scripts/new.js部分

...

const updateConfig = function(path, components) {
    // 更新 components.js 文件
    ...
}

const createPackages = function(componentName) {
    // 在 packages 目录下创建组件项目目录
    // 包括创建package.json、src/index.js、README.md、CHANGELOG.md等文件，及初始化文件内容
    ...
}

const createExample = function(componentName) {
    // 在 example 目录下创建组件示例
    // 包括创建index.vue，及初始化文件内容
    ...
}

...
```

<p style="text-align: center">
    <img src="http://hy-web2.bjcnc.scs.sohucs.com/doc/new.png?1" />
    <p style="text-align: center">执行创建脚本</p>
</p>

创建脚本除了创建目录和文件之外，我们还可以做一些文件内容初始化的工作，比如`README.md`写入组件名、使用说明、开发说明等标题，`examples/<component name>/index.vue`中写入template、对组件的引入等等。  

#### 格式化初始内容

在对文件写入时，我们遇到的一个问题就是文件初始内容格式与项目代码规范不同。

起初我们的解决方案是使用 ES6 的模板字符串，按照格式规范小心翼翼的写入初始化的模板，多余的空格、回车都会导致格式上的问题，不利于后期修改维护。  
引入模板文件替代模板字符串，会使模板编写相对容易一些，但是因为模板文件中会有一些非规范的标记，导致难以对其格式化，这依然是治标不治本的方案。

更好的方案是在脚本中引入`eslint`，`eslint`除了可以在命令行中运行，同样也可以在脚本中执行，`eslint`官网提供在NodeJS中运行的API文档。  
这样，我们就可以通过项目中的配置文件（比如`.eslintrc.js`）格式化文件的初始内容，即使以后我们改变了项目的代码规范，也不需要再变动创建文件的模板。

```javascript
// 根据配置初始化cli
const CLIEngine = eslint.CLIEngine;
// 需要注意的是比命令行中配置多添加`fix: true`才能格式化文件
const cli = new CLIEngine({
    ...require("../.eslintrc.js"),
    fix: true
});

// 执行eslint的fix
CLIEngine.outputFixes(
    cli.executeOnFiles([filePath])
);
```

#### 构建脚本

我们期望使用`@vue/cli`进行组件库中组件的构建，毕竟官方对自身更为了解。在官方文档中，只提到了在 shell 中执行 cli 提供构建功能，这样一次只能构建一个组件。  

我们可以通过脚本多次通过`child_process.exec`执行 shell 命令，来实现同时构建多个组件，这种方式在终端输出中会有文本颜色丢失现象。  

<p style="text-align: center">
    <img src="http://hy-web2.bjcnc.scs.sohucs.com/doc/exec.png" />
    <img src="http://hy-web2.bjcnc.scs.sohucs.com/doc/api.png" />
    <p style="text-align: center;">颜色丢失</p>
</p>

另一种方式就像上面使用`eslint`那样，我们可以在脚本中执行 shell 命令对应的API，麻烦的是`@vue/cli`文档中并没有提供 Node API 文档。通过阅读分析源码，我们在`@vue/cli`下发现`@vue/cli-service`中的`run`方法可以执行`build`。  

```javascript
// scripts/build.js

...
// 通过cli执行build命令
const vueCliService = require("@vue/cli-service");

const buildService = new vueCliService();

async function build() {
  for (let i = 0, len = components.length; i < len; i++) {
    const name = components[i].name
    // 传入对应各项参数
    await buildService.run(
      "build",
      {
        _: ["build", `${root}/packages/${name}/src/index.js`],
        target: "lib",
        name: `hy-${name}`,
        dest: `${root}/packages/${name}/dist`,
        // 生成格式: umd格式会同时成功demo.html commonjs,umd,umd-min
        formats: "commonjs,umd-min"
      }
    )
  }
}

...
```

使用 Node API 这种方式打包带来的另一个好处是我们可以实现更精细的控制。  
在我们的组件中，有一些是需要不同的打包配置的。在源码中，我们发现`@vue/cli-service`在实例化的时候，可以通过传入不同的路径作为参数，实现使用不同的配置执行`build`。在对每个组件执行`build`时，会先检测模块下是否存在配置文件，如果存在则用这个配置文件实例化`@vue/cli-service`，不存在就用根目录下配置文件。

```javascript
...

const packagePath = `${root}/packages/${pkgName}`;
const packageVueConfigJSPath = `${packagePath}/vue.config.js`;

// cli工作目录，支持package内自定义配置，默认使用根目录下vue.config.js
let cliWorkDict = fs.existsSync(packageVueConfigJSPath)
  ? packagePath
  : root;
console.log(">> vue.config.js", packageVueConfigJSPath);

let pkgCliService = new vueCliService(cliWorkDict);

...
```

## 版本管理

在完成创建、编写、构建过程之后，最后的步骤就是发布组件。

组件的发布版本号在自身目录下的`package.json`中，组件越来越多，组件之间可能存在相互依赖，这些导致管理维护版本号会是很痛苦的事情，而`lerna`可以很好的解决这个问题。

#### Lerna

`lerna`是一个`monorepo`的管理工具，提供了维护`monorepo`中的创建、管理、发布等功能。  
在创建和开发过程中，`lerna`可以帮助我们解决依赖安装问题，省去我们进入每个组件目录下执行`npm install`的麻烦。  
发布时，`lerna`主要帮助我们维护版本以及执行发布。`lerna`通过检测哪些组件中代码有变化，让我们选择更新主版本号、次版本号、还是修订版本号，帮助我们更新该组件版本号。之后，会帮我们提交到远程仓库以及npm仓库中。

<p style="text-align: center">
    <img src="http://hy-web2.bjcnc.scs.sohucs.com/doc/publish.png" />
    <p style="text-align: center;">版本选择</p>
</p>

#### Changelog规范

更新日志可以帮助用户和开发人员简单明确的知道项目中不同版本之间的变化。  
在我们的项目中，选择了人工以时间倒序的方式来维护`CHANGELOG.md`文件。内容主要包括版本号和变动列表，其中变动列表包括新增、变更、移除、修复等。新增用Added、变更用Changed、移除用Removed、修复用Fixed。

```markdown
<!-- 下面是一次版本变动的片段 -->
...

## v0.5.9
#### Added
- 增加70-92号表情
#### Fixed
- 没有对应表情时，可以显示对应文本

...
```

## 总结

至此，我们的Vue多组件仓库基本上搭建完成，最后做个总结。  

* 通过`@vue/cli`初始化了项目脚手架
* 创建了用于调试和演示的`examples`文件夹、用于存放各组件源码的`packages`文件夹
* 创建了存放创建/构建的脚本，以及存放脚本的`scripts`文件夹
* 使用`lerna`进行版本管理

另外，附下我们目前的开发流程：

1. 执行`npm run pkg-new <package name>`创建组件；
2. 在`packages/<package name>`下编写组件；
3. 在`examples/<packages name>/index.vue`中添加测试数据，运行调试；
4. 执行`npm run pkg-build [package name]`构建组件；
5. 执行`npm run pkg-publish`，选择版本号，发布组件。


---

最后，狐友前端组在类库/组件维护和NPM私服搭建上做了很多有趣的探索，如果大家有兴趣可以留言或与我们联系（spcfe@sohu-inc.com）。

