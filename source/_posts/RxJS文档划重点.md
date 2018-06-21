---
title: RxJS文档划重点
date: 2018-02-11
tags: [JavaScript]
categories:
- JavaScript
comments: true
---

### 基本概念

* RxJS的基本概念： Observable(可观察对象)，Observer(观察者)，Subscription(订阅者)，Operators(操作符)，Subject(主体)，Schedulers(调度器)

* 订阅Observable类似于调用函数

* Observable和函数的区别主要是Observable可以“返回”多个值，可以是同步也可以是异步

### Observable(可观察对象)

* Observable核心关注点：创建、订阅、执行、清理

* 创建： `Rx.Observable.create(function subscribe(observer) {...})`。Rx.Observable.create 是 Observable 构造函数的别名，它接收一个参数：subscribe 函数

* 订阅： `observable.subscribe(data => {...})`

* 执行： 创建中的`function subscribe(observer) {...}`中的...表示要执行的代码，订阅才会被执行。执行可以传递三种类型的值next、error、complete

* 清理： 订阅会返回一个subscription，通过`subscription.unsubscribe()`可以清理执行

### Observer(观察者)

* 观察者是由Observable发送的值的消费者（订阅中的回调方法）

* 观察者只是一组回调函数的集合，包括next、error、complete

* 观察者的使用： 将提供给Observable的subscribe方法`observable.subscribe(observer)`

* 参数可以传递为{next?: Function, error?: Function, complete?: Function}对象形式，也可以为fun1, func2, func3多个参数形式

### Subscription(订阅者)

* Subscription是表示可清理资源的对象，主要用来执行unsubscribe

* 通过add、remove方法可以组合多个Subscription用于一起执行unsubscribe

### Subject(主体)

* Subject是一种特殊类型的Observable，它允许将值多播给多个观察者

* BehaviorSubject会保存了发送给消费者的最新值。并且当有新的观察者订阅时，会立即从 BehaviorSubject那接收到“当前值”

```JavaScript
  var subject = new Rx.BehaviorSubject(0) // 0是初始值
  subject.subscribe({
    next: (v) => console.log('observerA: ' + v)
  })
  subject.next(1)
  subject.next(2)
  subject.subscribe({
    next: (v) => console.log('observerB: ' + v)
  })
  subject.next(3)
  // 输出
  observerA: 0 // 初始化0立即发送给了新的订阅A
  observerA: 1
  observerA: 2
  observerB: 2 // 保存的2会立即发送给了新的订阅B
  observerA: 3
  observerB: 3
```

* ReplaySubject可以发送旧值给新的观察者订阅（创建时指定缓存数量和缓存时间）

```JavaScript
  var subject = new Rx.ReplaySubject(3) // 为新的订阅者缓冲3个值
  subject.subscribe({
    next: (v) => console.log('observerA: ' + v)
  })
  subject.next(1)
  subject.next(2)
  subject.next(3)
  subject.next(4)
  subject.subscribe({
    next: (v) => console.log('observerB: ' + v)
  })
  subject.next(5)
  // 输出
  observerA: 1
  observerA: 2
  observerA: 3
  observerA: 4
  observerB: 2 // 缓存中的2、3、4被发送给订阅B
  observerB: 3
  observerB: 4
  observerA: 5
  observerB: 5
```

* AsyncSubject只有当 Observable执行完成时(执行了complete())，它才会将执行的最后一个值（上一个next）发送给观察者

### 操作符

* 操作符是Observable类型上的方法，是纯函数产生新的Observable，订阅输出Observalbe 同样会订阅输入Observable

### Scheduler(调度器)

* 调度器控制着何时启动subscription和何时发送通知

* 调度器类型主要包括null、Rx.Scheduler.queue、Rx.Scheduler.asap、Rx.Scheduler.async

* 如果没有提供调度器，会通过使用最小并发原则选择一个默认调度器

* 使用subscribeOn来调度subscribe()调用在什么样的上下文中执行

* 使用observeOn来调度发送通知的的上下文

### 其他

Subject和Observable一个疑问

```javascript
// Subject
const subject = new Rx.Subject()
let sub1 = null
let sub2 = null
function a () {
  sub1 = subject.subscribe({
    next: v => console.log('a: ' + v)
  })
  subject.next(1)
}
function b () {
  sub2 = subject.subscribe({
    next: (v) => console.log('b: ' + v)
  })
  subject.next(2)
  subject.complete()
  sub1.next(3) // 不会被订阅到
}
a()
b()

// Observable
let obs = null

const observable = Rx.Observable.create(function (observer) {
  obs = observer
  observer.next(1)
  observer.next(2)
  observer.next(3)
})

const a = observable.subscribe({
  next: x => console.log('a ' + x),
  error: err => console.error('a: ' + err),
  complete: () => console.log('a done')
})

const b = observable.subscribe({
  next: x => console.log('b ' + x),
  error: err => console.error('b: ' + err),
  complete: () => console.log('b done')
})

obs.complete() // 只会调用到b中的complete，输出b done
obs.next(4) // a、b都不会订阅执行到
a.next(5) // a会执行订阅
b.next(5) // b不会执行订阅
a.complete() // 输出a done
a.next(6) // 不会执行订阅
```