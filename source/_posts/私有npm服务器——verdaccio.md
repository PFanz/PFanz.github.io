---
title: 私有npm服务器——verdaccio
date: 2018-09-20
tags: [JavaScript, npm]
categories: 
- JavaScript
comments: true
---

### 介绍
搭建私有的npm服务器，可以同时安装私有npm包和官方npm包。

### 安装
```bash
npm i -g verdaccio
```

### 开启
```bash
$> verdaccio
warn --- config file  - /home/.config/verdaccio/config.yaml
warn --- http address - http://localhost:4873/ - verdaccio/3.0.1
```

通过上面提示可以知道，默认端口号为4873，配置文件为`/home/.config/verdaccio/config.yaml`。

### 配置
```yaml
storage: ./storage
auth:
  htpasswd:
    file: ./htpasswd
uplinks:
  npmjs:
    url: https://registry.npmjs.org/
packages:
  '@*/*':
    access: $all
    publish: $authenticated
    proxy: npmjs
  '**':
    proxy: npmjs
logs:
  - {type: stdout, format: pretty, level: http}
```

可以通过添加`listen: 0.0.0.0:4873`开启外网的访问。

### 其他

可以使用forever/pm2来守护verdaccio

可以通过`nrm`管理/切换npm源

[官方文档](https://verdaccio.org/zh-CN/)
