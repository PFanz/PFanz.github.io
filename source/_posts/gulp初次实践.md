gulp是前端构建工具，通过一系列gulp插件，可以实现实时监控、格式化代码、合并代码、压缩代码、测试功能等功能。

------

前提：node环境，会使用基本命令行

------

### 全局安装gulp
```
sudo npm install -g gulp
```
Windows环境下不需要sudo命令

### 项目搭建
#### 第一步，包管理文件 package.json
通过cd命令进入项目目录，执行`npm init`，之后可以一路的回车来创建`package.json`文件。关于此配置文件的内容挺简单的，可以自己看下，然后搜索下。
#### 第二步，寻找、安装所需要的插件
项目目录安装gulp，并写入配置文件package.json中。
```
npm install --save-dev gulp
```
这样会把gulp的依赖写入到package.json文件中。

#### 添加需要的各个gulp插件
比如这里我用到了gulp-clean-css用于css压缩、gulp-uglify用于js压缩、gulp-watch用于文件监控、gulp-sass用于sass编译。
安装各插件(方法相同)
```
npm install --save-dev gulp-uglify
```
#### 第三步，配置gulp任务
在项目目录下创建文件`gulpfile.js`，用于编写gulp任务。
gulp基础语法比较简单，管道方式也很容易理解。
这里我的项目目录如下：
```
├── dist
│   ├── css
│   ├── img
│   └── js
├── gulpfile.js
├── index.html
├── node_modules
│   ├── eruda
│   ├── gulp
│   ├── gulp-clean-css
│   ├── gulp-sass
│   ├── gulp-uglify
├── package.json
└── src
    ├── css
    ├── img
    ├── js
    ├── libs
    └── sass
```
其中src文件夹中是源文件，
dist文件夹下是项目用到的压缩后的js、css文件。

gulp任务配置文件gulpfile.js如下：
```javascript
var gulp = require('gulp'),
		cleanCSS = require('gulp-clean-css'),
		uglify = require('gulp-uglify'),
		watch = require('gulp-watch'),
		sass = require('gulp-sass');

gulp.task('default', function() {
	// 默认任务代码
});

// 压缩文件
gulp.task('minfile', function() {
	// sass
	gulp.src('src/sass/*.scss')
		.pipe(sass())
		.pipe(gulp.dest('src/css'))
		.pipe(cleanCSS())
		.pipe(gulp.dest('dist/css'));
	// js
	gulp.src('src/js/*.js')
		.pipe(uglify())
		.pipe(gulp.dest('dist/js'));
	// img
	gulp.src('src/img/*')
		.pipe(gulp.dest('dist/img'));
});

// 监听
gulp.task('watchFile', ['minfile'], function() {
	gulp.watch('src/**/*', ['minfile']);
});

gulp.task('default', ['minfile', 'watchFile']);
```
gulp API只有五个：task，src，dest，watch和run。
task用于定义任务；src用于表示源文件路径；dest用于输出，写入新文件；watch用于监听文件变化；run用于执行任务。
ps:上面的pipe表示管道。
#### 第四步，开启任务
项目目录下，执行`gulp`即可。之后就可以开始编写代码了。

---------

第一次对gulp的尝试，也是第一次对构建工具的尝试，其中只是用到了一些最简单也是最常用的插件，给我的感觉就是省去了每次手动编译、压缩等操作，这次使用就像一次Hello World。