---
title: Dva中的handleAction
date: 2017-03-21
tags: javascript React
categories: 
- React
comments: true
---

Dva是支付宝基于React、redux、redux-saga和react-router的轻量级框架，代码不多，主要是实现了一些现有应用的一层封装，使得编写React应用编写更清晰方便。  
最近在学习使用Dva，主要学习了之前没有接触redux-saga的使用，熟悉了Dva的项目结构和开发流程。这两天试着看了下源码，又被虐得不要不要的。

在Dva中有个handleActions.js，只有三十行代码，三个函数，却是困扰我半天。只能说自己还是太菜、太初级。

从文件名handleActions显然是处理redux中的actions的，redux中通过触发action来执行reducer（就是将action传递给reducer），返回新的state以实现页面的更新。

出口函数handleActions是这样的
```javascript
function handleActions(handlers, defaultState) {
  const reducers = Object.keys(handlers).map(type => handleAction(type, handlers[type]));
  const reducer = reduceReducers(...reducers);
  return (state = defaultState, action) => reducer(state, action);
}
```
参数handlers、defaultState分别表示处理函数和默认state。从使用方法和函数中的定义可以看出handlers和defaultState都是对象。  
第一句，Object.keys(handlers).map用于对对象的循环，虽然看到Object.keys能够知道是什么作用，但是自己在编写代码时会忘记该方法而使用for...in，for...in循环包括原型链上的属性显然要差些。接着调用了handleAction，传入了type（也就是key）以及处理函数handlers[type]。
```javascript
function handleAction(actionType, reducer = identify) {
  return (state, action) => {
    const { type } = action;
    if (type && actionType !== type) {
      return state;
    }
    return reducer(state, action);
  };
}
```
handleAction接受参数actionType和reducer，reducer默认值是一个直接返回原参数的函数。最后返回的是一个函数，函数中使用了actionType以及reducer，这属于闭包的应用。这个函数如果使用的话是这样的`handleAction(actionType, reducer)(state, action)`，这个应该属于函数的柯里化，对柯里化看了一些资料，但是依然一知半解，这里使用柯里化的目的应该是保存变量actionType和reducer。

回到handleActions函数中，reducers最后就被保存为了元素是下面这样函数的数组，其中的actionType和reducer是通过闭包保存不同的内容的。
```javascript
(state, action) => {
  const { type } = action;
  if (type && actionType !== type) {
    return state;
  }
  return reducer(state, action);
}
```

第二句执行函数reduceReducers
```javascript
function reduceReducers(...reducers) {
  return (previous, current) =>
    reducers.reduce(
      (p, r) => r(p, current),
      previous,
    );
}
```
同样又是返回一个函数保存到reducer中，reducer这个函数中通过闭包保存了函数数组reducers。

第三句是return，返回值依然是一个函数。函数中调用reducer，并且传参state和action。  
这样我们可以知道reducer函数中previous表示的state，current表示action。  
然后通过reducers.reduce循环执行所有的reducers，传参为state和action。然后在handleAction返回参数中，根据action.type和之前保存的actionType比较决定是否执行之前保存的reducer来处理state。如果type不同，不对state进行处理直接返回。

功能并不是特别复杂，相当于reduceReducers对reducers进行循环，handleAction对action进行判断。主要困扰集中在了对reduce使用上的不熟悉，柯里化的复杂，闭包的实际应用等。

