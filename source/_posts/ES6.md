From [点击这里](http://mp.weixin.qq.com/s?__biz=MzAxMjA5ODQwMQ==&mid=2455058700&idx=1&sn=9361036ef5dc0c27cafb0612d495a85c)
## ES6
### 定义变量：let、const
* let、const声明变量为块级作用域，var声明为函数作用域
* let声明不具备变量提升，const声明具备
* const用于声明常量，必须声明时赋值
* let、const声明变量不能再次声明覆盖
### 函数声明：箭头函数
缘由

ps:
```
// 六种语言中的简单函数示例
function (a) { return a > 0; } // JS
[](int a) { return a > 0; }  // C++
(lambda (a) (> a 0))  ;; Lisp
lambda a: a > 0  # Python
a => a > 0  // C#
a -> a > 0  // Java
```
1. 情况一 a => a > 0   相当于 function(a) { return a > 0 }  tag:单个参数、直接返回
2. 情况二 (a, b) => a + b  相当于 function(a, b) { return a + b }  tag:多个参数、直接返回
3. 情况三 （a, b）=> { a + b ... }  相当于 function(a, b) { a + b ... } tag:函数体，返回需要自己写return语句
4. 情况四  (a, b) => ({a: 'a'}) 相当于 function(a, b) { return {a: 'a'} } tab:直接返回对象，因为=>后面的{}会被理解为函数体，添加()才会被理解为对象

* 箭头函数相当于匿名函数的简写
* 箭头函数的this总是和外层函数的this指向相同。
	eg.
```javascript
// ES5
function Person() {
  var self = this;
  self.age = 0;

  setInterval(function growUp() {
  	// this会指向windows
  	// 所以用self保存了this
    self.age++;
  }, 1000);
}
// ES6
function Person(){
  this.age = 0;

  setInterval(() => {
    // |this| 指向 person 对象
    this.age++;
  }, 1000);
}

var person = new Person();
```
### for...of 
1. for...of  遍历数组,遍历字符串,对象没有该方法
```javascript
let nicknames = ['di', 'boo', 'punkeye'];
for (let nickname of nicknames) {
  console.log(nickname);
}
Result: di, boo, punkeye
```
2. for...in  遍历对象，数组也能够使用该方法
```javascript
let nicknames = ['di', 'boo', 'punkeye'];
nicknames.size = 3;
for (let nickname in nicknames) {
  console.log(nickname); // 0 1 2 size
}
```
### 类Class
```javascript
// ES5 定义类
function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};
// 相当于 ES6中
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  // 不需要添加function，方法和方法之间不能用逗号隔开
  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
tyof Point // function  说明Class只是一个语法糖
Point === Point.prototype.constructor // true
```
* new className()会自动调用constructor()
* class定义的类只能通过new操作符，不能直接调用
* 通过class只能定义在construtor中的共有属性，不能定义私有属性，定义的方法也都是相当于定义在prototype上的共有方法
* 与ES5中定义在prototype不同在于不可枚举
* 与ES5相同，所有实例共享原型。所以通过实例的__proto__属性可以为Class添加方法
* 不存在变量提升，Class定义与ES5不同

### 继承extends
在使用extends继承时，子类的constructor中必须先调用super()方法，也就是父类的构造方法。

### 对象超类
```javascript
var parent = {
  foo() {
    console.log("Hello from the Parent");
  }
}

var child = {
  foo() {
    super.foo();
    console.log("Hello from the Child");
  }
}

Object.setPrototypeOf(child, parent); // ES6设置对象原型的方法
child.foo(); // Hello from the Parent
             // Hello from the Child
```
### Promise
Promise对象有三种状态：Pending、Resolved，Rejected，外界无法改变状态，状态一旦改变就不会再变。
```javascript
var promise = new Promise(function(resolve, reject) {
	// ... some code

	if(/* 异步操作成功 */) {
		resolve(value);  // 将promise状态变为Resolved
	} else {
		reject(error);
	}
});

promise.then(function(value) {
	// success
}, function(error) {
	// failure
})
```
ps:

一个用Promise实现Ajax操作的例子
```javascript
var getJSON = function(url) {
	var promise = new Promise(function(resolve, reject) {
		var client = new XMLHttpRequest();
		client.open('GET', url);
		client.onreadystatechange = handler;
		client.responseType = 'json';
		client.setRequestHeader('Accept', 'application/json');
		client.send();

		function handler() {
			if(this.readyState !== 4) {
				return ;
			}
			if(this.status === 200) {
				resolve(this.response);
			} else {
				reject(new Error(this.statusText));
			}
		};
	});

	return promise;
}
```
### 模板语法和分隔符
```javascript
let user = 'Barret';
console.log(`Hi ${user}!`); // Hi Barret
```

### 对象扩展
```javascript
function getCar(make, model, value) {
  return {
    // 简写变量
    make,  // 等同于 make: make
    model, // 等同于 model: model
    value, // 等同于 value: value

    // 属性可以使用表达式计算值
    ['make' + make]: true,

    // 忽略 `function` 关键词简写对象函数
    depreciate() {
      this.value -= 2500;
    }
  };
}
```
### 对象和数组解构
```javascript
function foo() {
  return [1,2,3];
}
let arr = foo(); // [1,2,3]

let [a, b, c] = foo();
console.log(a, b, c); // 1 2 3

function bar() {
  return {
    x: 4,
    y: 5,
    z: 6
  };
}
let {x: x, y: y, z: z} = bar();
console.log(x, y, z); // 4 5 6
```
### 迭代器
通过[Symbol.iterator]()定义一个对象的迭代器
```javascript
var arr = [11,12,13];
var itr = arr[Symbol.iterator]();

itr.next(); // { value: 11, done: false }
itr.next(); // { value: 12, done: false }
itr.next(); // { value: 13, done: false }

itr.next(); // { value: undefined, done: true }
```

### 扩展操作符...
貌似只能用于参数传递
1. 情况一 
```javascript
function foo(x,y,z) {
  console.log(x,y,z);
}

let arr = [1,2,3];
foo(...arr); // 1 2 3
```
2. 情况二
```javascript
function foo(...args) {
  console.log(args);
}
foo( 1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]
```