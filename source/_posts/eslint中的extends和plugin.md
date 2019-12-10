---
title: eslint中的extends和plugin
date: 2019-10-11
tags: [JavaScript]
categories:
- JavaScript
comments: true
---

## extends

一个配置文件可以被基础配置中的已启用的规则继承。

extends 属性值可以是：

指定配置的字符串(配置文件的路径、可共享配置的名称、eslint:recommended 或 eslint:all)

字符串数组：每个配置继承它前面的配置

## plugin

插件 是一个 npm 包，通常输出规则。一些插件也可以输出一个或多个命名的 配置。要确保这个包安装在 ESLint 能请求到的目录下。

extends 属性值可以由以下组成：

plugin:
- the package name (from which you can omit the prefix, for example, react)
- 包名 (省略了前缀，比如，react)
- /
- 配置名称 (比如 recommended)