---
title: 了解Flutter
date: 2018-08-13
tags: [移动端, Flutter]
categories: 
- 移动端
comments: true
---

### 跨平台解决方案

目前跨平台解决方案主要有WebView（Cordova）、转化为系统平台自带控件（RN），Flutter采取得的是使用自身的高性能渲染引擎(Skia)自绘。

WebView: 界面通过HTML、CSS； 与原生交互通过桥接。    
假跳转的请求拦截、弹窗拦截（alert、prompt、confirm）、JS上下文注入（iOS的JavaScriptCore、Android的addJavaScriptInterface）。

React Native： 界面通过React技术实现； 与原生交互通过桥接。

Flutter: 界面通过自绘； 与原生交互，通过转换为原生代码。

<img src="https://gw.alicdn.com/tfs/TB188eCw4GYBuNjy0FnXXX5lpXa-1271-922.png">

### Dart语言

1. 面向对象语言，入口函数main函数。
2. 所有变量的值都是对象，包括数字、字符串`1.toDouble();`。所有对象继承Object，未初始化的所有变量为null。
3. 强类型语言，支持类型推断。
4. 不具有public，protected和private关键字，以_开头的相当于私有。
5. new可以省去。
6. 级联运算符(..)，返回前一个值。
```
// JavaScript
let arr1 = [1, 2, 3]
arr1.push(4)
let arr2 = arr1

// Dart
List<int> list1 = [1, 2, 3];
List<int> list2 = list1..insert(3, 4);
```
7. 提供??、?.等操作符。(js中&&，angular模板中的?.)
8. 异步Future，可以使用async和await。(then、catch)
9. 支持AOT和JIT运行方式（热重载）。

### Flutter

1. 所有内容继承自`Widget`。
2. 主要分为`StatefulWidget`与`StatelessWidget`。
3. 通过调用`setState`重新渲染组件。
4. 布局支持flex、grid等。
5. 组件丰富、自带icon。
6. 通过`plugin`实现原生调用。
7. 热重载，开发方便。

生命周期： 

    initState，build, didUpdateWidget(oldWidget), deactivate， dispose等。

全局密钥：

    通过GlobalKey可以获得部件。