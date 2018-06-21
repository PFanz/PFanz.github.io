---
title: RxJS入门
date: 2018-02-13
tags: [JavaScript]
categories:
- JavaScript
comments: true
---

### RxJS

定义： 通过使用observable序列来编写异步和基于事件的程序

核心思想： 函数式编程 + 响应式编程

#### 原因

* Reactive Programming的兴起

* Observable标准化

* 多语言支持

#### 解决的问题（异步带来的问题）

* 竞态条件

* 内存泄露

* 复杂的状态

* 异常处理

* ps: 统一API

#### 函数式编程

* 条件： 函数是一等公民

```JavaScript
// 可以被赋值
var sayHello = function () {
  console.log('hello')
}
// 可以当做参数
document.getElementById('a').click(sayHello)
// 可以被函数返回
function getFunc () {
  return function (content) {
    console.log(content)
  }
}
```

* 纯函数

```JavaScript
// 非纯函数（有副作用）
function addArray (array, item) {
  array.push(item)
  return array
}
// 非纯函数（依赖外部变量）
let array = [1, 2, 3]
function addArray(item) {
  let result = []
  result.push(...array)
  result.push(item)
  return result
}
// 纯函数（创建了变量，开辟了新的内存空间，也是副作用，所以要柯里化）
function addArray (array, item) {
  let result = []
  result.push(...array)
  result.push(item)
  return result
}
```

#### 观察者模式（生产者推送数据）

```JavaScript
class Producer {

  constructor() {
    this.listeners = []
  }

  addListener(listener) {
    if(typeof listener === 'function') {
      this.listeners.push(listener)
    } else {
      throw new Error('listener必须是function')
    }
  }

  removeListener(listener) {
    this.listeners.splice(this.listeners.indexOf(listener), 1)
  }

  notify(message) {
    this.listeners.forEach(listener => {
      listener(message)
    })
  }
}
```

#### 迭代器模式（消费者拉取数据）

```JavaScript
class IteratorFromArray {
  
  constructor(arr) {
    this._array = arr
    this._cursor = 0
  }
  
  next() {
    return this._cursor < this._array.length ? 
      {
        value: this._array[this._cursor++],
        done: false 
      } : { done: true }
  }
}
```

ps:

| |单个值|多个值|
|:----:|:----:|:----:|
|拉取|Function|Iterator|
|推送|Promise|Observable|

Observable具备生产者推送数据的能力，又拥有序列处理数据的方法

#### Observable

```JavaScript
// 声明一个可观察对象
var observable = Rx.Observable
  .create(function(observer) {
    observer.next('Hello')
    observer.next('World')
    observer.complete()
    observer.next('not work')
  })
  
// 声明一个观察者，具备next、error、complete方法
var observer = {
  next: function(value) {
    console.log(value)
  },
  error: function(error) {
    console.log(error)
  },
  complete: function() {
    console.log('complete')
  }
}

// 用观察者订阅可观察对象
observable.subscribe(observer)
```

#### Observable实现细节

```JavaScript
function subscribe(observer) {
    observer.next('Hello')
    observer.next('World')
}

subscribe({
  next: function(value) {
    console.log(value)
  },
  error: function(error) {
    console.log(error)
  },
  complete: function() {
    console.log('complete')
  }
})
```

#### 操作符

* 创建操作符、转换操作符、过滤操作符、组合操作符、多播操作符、错误处理操作符、工具操作符、条件和布尔操作符、数学和聚合操作符

```JavaScript
var people = Rx.Observable.of('World', 'China')

function map(source, callback) {
  // 返回new Observable对象
  return Rx.Observable.create((observer) => {
    // 订阅原本的Observable对象
    return source.subscribe(
      (value) => { 
        try{
          observer.next(callback(value))
        } catch(e) {
          observer.error(e)
        }
      },
      (err) => { observer.error(err) },
      () => { observer.complete() }
    )
  })
}

var helloPeople = map(people, (item) => ' Hello~' + item)

helloPeople.subscribe(console.log)
```

#### Subject

```JavaScript
var source = Rx.Observable.interval(1000).take(3)

var observerA = {
  next: value => console.log('A next: ' + value),

  error: error => console.log('A error: ' + error),

  complete: () => console.log('A complete!')
}

var observerB = {
  next: value => console.log('B next: ' + value),

  error: error => console.log('B error: ' + error),

  complete: () => console.log('B complete!')
}

var subject = {
  observers: [],

  addObserver: function(observer) {
    this.observers.push(observer)
  },

  next: function(value) {
    this.observers.forEach(o => o.next(value))    
  },

  error: function(error){
    this.observers.forEach(o => o.error(error))
  },

  complete: function() {
    this.observers.forEach(o => o.complete())
  }
}

subject.addObserver(observerA)

source.subscribe(subject)

subject.addObserver(observerB)
```

* Subject同时是Observable又是Observer

* Subject会对内部的observers数组进行组播


