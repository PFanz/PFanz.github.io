---
title: 为gulp添加更多
date: 2016-07-23
tags: javascript 构建工具 gulp
categories: 
- javascript
comments: true
---

有了之前的gulp经验，使用其他gulp工具就是很简单的工作了。

> ps:	以下代码前均包含`var gulp = require('gulp')`

> 类似`sass()`表示前面包含`var sass = require('gulp-sass')`

--------------

[gulp-autoprefixer](https://www.npmjs.com/package/gulp-autoprefixer)：应该属于css后处理工具，在编写css完成之后，会根据我们的配置自动按需添加浏览器前缀。

任务事例：
```javascript
gulp.task('sass', function() {
	return gulp.src('src/sass/*.scss')
					.pipe(sass())
					.pipe(autoprefixer('last 2 versions', 'ios 6', 'android 4'))	//autoprefixer
					.pipe(gulp.dest('src/css'))
					.pipe(cleanCSS())																							//css压缩工具，前面介绍有
					.pipe(gulp.dest('dist/css'))
});
```
其中`autoprefixer('...','...','...')`为简写形式，其中`last 2 versions`表示兼容所有浏览器最新两个版本。
完整形式应该为`autoprefixer({ 
	browsers: ['last 2 versions', 'ios 6'],
	cascade: true
})`

***

[gulp-imagemin](https://www.npmjs.com/package/gulp-imagemin):图片压缩工具。

任务事例：
```javascript
gulp.task('imageMin', function() {
	gulp.src('src/img/*')
		.pipe(imagemin())
		.pipe(gulp.dest('dist/img'));
});
```
对于imagemin()进行图片压缩，可以结合使用`gulp-cache`对图片进行缓存。具体用法，我也没研究太懂 :joy:

***

[browser-sync](https://www.npmjs.com/package/browser-sync):属于综合测试的工具，可以实现页面自动刷新、多屏同步操作等功能。

任务实例：
```javascript
//首先browser-sync的引用，官方推荐使用create()方法
var browserSync = require('browser-sync').create();

gulp.task('serve', ['sass'], function() {
	browserSync.init({
		server: './'
	});

	gulp.watch('src/sass/*.scss', ['sass']);
	gulp.watch('*.html').on('change', browserSync.reload);
});
```
其中`server: './'`会将当前目录作为服务器访问地址<br/>
`gulp.watch('src/sass/*.scss', ['sass'])`会监听*.scss文件，执行`sass`任务，并且实时反应到浏览器上。
`gulp.watch('*.html').on('change', browserSync.reload)`不同之处在于，会执行reload方法，重新加载页面。

--------

对于工具，我觉得更多的是直接拿过来使用，而不是非要去研究清楚每个API的作用。当然，特别感兴趣的也可以尝试研究工具，甚至是自己动手去制造这些工具。