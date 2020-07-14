---
title: Serverless入门小结
date: 2020-06-05
tags: [杂记]
categories:
- 部署
comments: true
---

## 定位

运维（/云厂商）提供的解决方案（/产品），帮助开发者解决服务器相关的问题，比如部署、运行状态监控等。

## 目标

了解如何使用

1. 了解历史背景
2. 了解优劣势
3. 了解可能带来的变化

## 历史背景

1. IaaS(Infrastructure as a Service) 基础设施即服务 
2. PaaS(Platform as a Service) 平台即服务
3. SaaS(Software as a Service) 软件即服务
4. CaaS(Container-as-a-Service) 容器即服务

![image](https://note.youdao.com/yws/res/8811/EB00F1CDEF0C4DEE876685DF3630C7CF)

#### 定义

> “无服务器架构是基于互联网的系统，其中应用开发不使用常规的服务进程。相反，它们仅依赖于第三方服务（例如AWS Lambda服务），客户端逻辑和服务托管远程过程调用的组合。”  --AWS

![image](https://note.youdao.com/yws/res/8815/8D5B18830590440F8BD5B2612B02DAB2)

## 优势

- 毫秒级自动缩/扩容能力
- 只有使用才付费
- 运维成本
- 隔离操作系统，乃至更底层的技术细节。

![image](https://note.youdao.com/yws/res/8817/BE16F424DAFF483BBB78151B275A9ACA)

## 问题

Serverless 应用严重依赖于特定的云平台、第三方服务

![image](https://note.youdao.com/yws/res/8820/069B1D64645E4FBDB953EFB6DBE71928)


## 使用

![image](https://note.youdao.com/yws/res/8822/7C557E5B87C64546A4C937746A2164DF)

#### FaaS

#### 触发器

- API 网关触发器
- COS 触发器
- 定时触发器
- CMQ Topic 触发器
- CKafka 触发器
- 云 API 调用

#### Serverless Framework命令行工具

#### Serverless Components

- deploy
- remove