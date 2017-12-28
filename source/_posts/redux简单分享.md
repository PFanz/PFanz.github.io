---
title: Redux简单分享
date: 2017-12-28
tags: [JavaScript, React]
categories:
- JavaScript
comments: true
---

Redux是JavaScript实现的状态容器，提供了可预测化的状态管理。

为什么需要状态容器？  

1. 随着页面的复杂度不断增加，需要将页面的状态进行统一的管理。这些状态更多的是指那些和数据无关的数据，比如：一个简单的注册页面，包含了用户名验证的状态、密码验证的状态、确认密码验证的状态等等。  
2. 在React中，一个页面是由各种组件组成的，而React将组件看成是个状态机，一开始会有个初始状态，用户的交互导致状态的编导，从而重新渲染UI。（在React中通过setState修改state，触发UI的重新渲染）。
3. 因为存在父子组件的通信、异步等问题，状态的情况会更加复杂，很难去预测。

Redux的核心

Action，就是定义一个行动，被定义为包含type属性的对象，用于声明需要修改的state。可以理解为Action就是描述一件事情的发生的。

```JavaScript
let nextTodoId = 0
const addTodo = text => {
  return {
    type: 'ADD_TODO',
    id: nextTodoId++,
    text
  }
}
```
感觉有点类似数据库的插入，type表示要执行插入，id这里是个自增的整型，text为内容。

Reducer，通过接受Action来更新state，这里的更新并不能直接改变state的值，而是返回一个新的state。(纯函数，为了实现状态的预测)

```JavaScript
const todos = (state = [], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          id: action.id,
          text: action.text,
          completed: false
        }
      ]
    default:
      return state
  }
}
```

Store，用于存放所有state。

```JavaScript
const store = Store.createStore(todos)

```

Redux中的数据流

![Redux数据流](./images/redux简化数据流.jpeg)


```JavaScript
const Store = (() => {

  // 首先是store需要存放state，并且该state应该私有。
  let currState = {}
  const getState = () => currState

  // 提供触发Action的方法
  // setState(todos(getState, addTodo('abc')))
  // 但是这样显然就会造成state的混乱
  let currReducer = () => {}
  const dispatch = (action) => {
    currState = currReducer(currState, action)
  }

  // 将reducer传入store中
  const createStore = function (reducer) {
    currReducer = reducer
    return this
  }

  return {
    getState,
    dispatch,
    createStore
  }
})()
```
