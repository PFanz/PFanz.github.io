---
title: GitHub大小写问题
date: 2017-04-18
tags: [搭建博客， Hexo]
categories: 
- 杂记
comments: true
---

经过几天的整理，将博客添加了网易云音乐，加入了谷歌搜索以及谷歌分析，虽然访客重来都是0，但是在用Google搜索到自己的博客的时候，还是挺高兴的。  
结果第二天谷歌就发来邮件，检测到有几个死链，经过排查发现是hexo生成文件夹大小写的问题，之前没有太主要大小写问题，导致tags和categories中存在大小写共存，导致链接混乱的问题。  
经过多次删除GitHub上分支，重新生成、部署Hexo依然无法解决大小写问题（本地生成、预览是没有问题的），问题应该从本地Git上出发。

在Mac OS、Windows系统下，文件名是不区分大小写的，也就是你在同一个目录下，使用大小写不同的命名不能建立两个文件。Git在默认情况下，对大小写也是不敏感的，比如将git管理的一个文件名修改大小写，`git status`并不会检测到文件有变化。在GitHub下，文件名大小写会被区分。

第一步，要做的是修改git的配置，使其对大小写敏感，这样才能修改github上的大小写问题。
```bash
git config core.ignorecase false
```
ps: 通过`git config -l`可以列出当前config

第二步，将Hexo在GitHub上的master分支拉去下来（这里看自己的分支所在，我的是默认分支为hexo用于存储Hexo原始项目，master用于部署Hexo项目）。
```bash
git pull master:master
git checkout master
```

第三步，移除错误大小写文件，比如我这里是将`./tags/javascript/index.html`移除，改为`./tags/JavaScript/index.html`
```bash
git rm tags/javascript/index.html
```
这里需要注意的是pull下来的master中的tags文件夹只有JavaScript，可以先将文件夹下的index.html另存一份。然后这里执行`git rm`中的文件夹名字应该是小写的javascript，如果使用JavaScript会找不到该文件，这说明在git的保存中是区分大小写的。

第四步，就是将另存的index.html剪切过来，并执行正常的`add commit push`就可以了。至此，Hexo中的错误大小写路径就能修改正常。同样，方法应该适用于GitHub其他项目。