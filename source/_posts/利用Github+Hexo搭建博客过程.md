---
title: 利用Github+Hexo搭建博客过程
date: 2016-06-09 22:11:35
tags: [Hexo, 搭建博客]
categories: 
- 杂记
comments: true
---

　　有阵子没有静下心来做点什么了，希望通过博客，可以让自己多静下心来做些东西，学习下技术，记录一下过程。
　　因为最近一直在学习和使用Github，希望通过Github来建立自己的博客，并且还能刷下自己Github上提交的贡献-.-
　　总得过程还是比较容易的，因为自己有使用Github，电脑上也安装配置有nodejs，也看过markdown的一些语法，所以搭建起来，实现基本功能并没有花费多少时间。
　　第一步，安装nodejs和Git。
　　第二步，安装Hexo,和其他的包一样，通过`npm install-g hexo`来安装。<!--more-->
　　第三步，创建一个用于存放Hexo的文件夹，在该文件夹目录下执行hexo init。
　　第四步，执行`hexo g`(等同于`hexo generate`)生成静态页面，执行`hexo s`(等同于`hexo server`)启动本地服务进行预览，这样浏览器里可以输入`http://localhost:4000`进行访问。我第一次访问的时候，因为4000端口被占用，页面打不开，关闭占用端口后，正常。
　　第五步，配置博客。在Hexo目录配置文件_config.yml中可以配置诸如标题、副标题、作者、语言、主题、部署等等配置。
　　第六步，配置主题。搜索Hexo主题会有很多，并且都有使用说明。一般流程就是通过git clone复制到自己的目录下，在Hexo目录下的_config.yml文件中配置`theme`属性，之后就是主题配置文件(位于themes/*/_config.yml)进行个性化配置。
  
　　之后，就是把博客放到Github上去了。
　　首先是在个人GIthub上建立新的仓库，仓库名为`username.github.io`，比如我的Github帐号是[PFanz](http://github.com/PFanz),仓库名就是`PFanz.github.io`。
　　在Hexo配置文件_config.yml中配置`deploy`中各项，比如：
```
deploy:
  type: git
  repo: https://github.com/PFanz/PFanz.github.io.git
  branch: master
```
　　然后执行命令`npm install hexo-deployer-git --save`，便可以通过`username.github.io`访问到该博客。
　　写博客可以通过命令`hexo new"titleName"`，在Hexo目录下`source/_posts`新建文件titleName.md，也可以自己手动建立。手动建立的话，需要自己加入头
```
---
title: titleName
date: 2016-06-09 22:11:35
tags: 标签
categories: 
- 分类名称
---
  ```
  
　　每次部署按一下三步
```
hexo clean
hexo g
hexo d
```
  
  

---
　　虽然说基本功能已经搭建，感觉还有不少地方值得去探索，以实现更个性化，更漂亮的博客。