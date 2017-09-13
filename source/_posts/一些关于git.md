---
title: 一些关于git
date: 2017-09-13
tags: git
categories:
- git
comments: true
---

.git文件夹下  
  1. 不重要：
    * config文件保存项目特有的配置  
    * info目录，不希望在.gitignore文件中管理的忽略模式的全局可执行文件  
    * hooks钩子脚本
  2. 重要：
    * HEAD文件指向当前分支
    * index文件保存暂存区信息
    * objects目录存储所有数据
    * refs目录存储指向数据(分支)的提交对象的指针

objects文件夹下

  主要对象：（通过键值对进行保存，键为hash，值为对象）
  1. blob对象（文件内容）
  2. tree对象（用于包含blob对象，组织结构。版本、分支指向根tree对象）
  3. commit对象 （提交信息）
  4. tag对象
  5. ... ...

Packfiles：  
  将blob压缩，当前版本保存完整信息，其他版本保存差量信息。大部分情况是自动完成，可以调用git gc手动执行
