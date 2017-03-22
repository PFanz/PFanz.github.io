---
title: 面向对象的JavaScript(封装)
date: 2016-06-17 20:23
tags: [JavaScript, 面向对象]
categories: 
- JavaScript
comments: true
---

　　面向对象的基本特征：封装、继承、多态。
　　封装就是把对象的属性和方法结合成在一些，也可以隐藏对象的属性和实现细节，所以封装后的对象一般有私有属性、私有方法、公有属性、公有方法等。

--------------------

### 1.最初的javascript代码是这样的
```javascript
function checkName() {
	
}
function checkEmail() {
	
}
function checkPassword() {
	
}
```
调用时候，直接使用函数名checkName()。这样编写相当于创建多个全局变量：checkName、checkEmail、checkPassword。

### 2.使用对象封装(原始模式)
```javascript
var CheckObject = {
	checkName: function() {

	},
	checkEmail: function() {

	},
	checkPassword: function() {

	}
};
```
调用时，通过对象名调用，CheckObject.checkName()。缺点是只能够通过CheckObject使用，不能生成新的实例。

### 3.返回新的对象(原始模式改进)
```javascript
var CheckObject = function() {
	return {
		checkName: function() {

		},
		checkEmail: function() {

		},
		checkPassword: function() {

		}
	}
};
```
调用方式
```javascript
var a = CheckObject();
a.checkName();
```
每次调用CHeckObject都会产生新的对象，这样每个人在使用的时候互相之间不会影响，但是新生成的实例与实例之间没有关联，与CheckObject也没有关联。

### 4.new操作符，能将方法中this指向新的实例(构造函数模式)
```javascript
var CheckObject = function() {
	this.checkName = function() {

	};
	this.checkEmail = function() {

	};
	this.checkPassword = function() {

	};
};
```
调用方式
```javascript
var a = new CheckObject();
a.checkName();
```
所有通过new生成的实例对象，都包含constructor属性，这个属性都会指向CheckObject。`a.constructor == CHeckObject`每个新生成的实例中都会包含checkName、checkEmail、checkPassword方法，这些方法都是相同的，可以通过原型链保存在prototype属性中，这样这些方法就只会存储一遍。
	ps: new首先会创建一个空的对象。然后将新对象的_proto_指向调用函数的prototype属性。 最后将调用函数中的所有this指向新对象。

### 5.利用prototype节省内存(prototype模式)
```javascript
var CheckObject = function() {
	
};
CheckObject.prototype = {
	checkName: function() {

		return this;  //链式使用
	},
	checkEmail: function() {

		return this; //链式使用
	}，
	checkPassword: function() {

		return this; //链式使用
	}
};
```
调用方法与4.相同。

### 6.更像一个类
```javascript
var Book = function(id, name, price) {
	// 私有属性  (外界不能直接访问、调用)
	var num = 0;
	// 私有方法
	function checkId() {}
	// 对象公有属性  (对象可以访问、调用)
	this.id = id;
	// 特权方法  (对象可以访问、调用，并且操作私有属性、方法)
	this.getName = function() {};
	this.setName = function() {};
	this.getPrice = function() {};
	this.setPrice = function() {};
};
// 类静态公有属性  (类可以访问、调用)
Book.isChinese = true;
// 类静态公有方法
Book.resetTime = function() {};
Book.prototype = {
	// 静态公有属性  (对象可以访问、调用，并且只保存一份)
	isJSBook: false,
	// 静态公有方法
	display: function() {}
};
```

### 7.利用闭包来实现
```javascript
var Book = (function() {
	// 静态私有
	var bookNum = 0;
	function checkBook() {};
	// 创建类
	function _book(id, name, price) {
		// 私有
		var name,price;
		function checkID(){}
		this.sayNum = function() {
			console.log(bookNum);
		};
		this.setNum = function(number) {
			bookNum = number;
		};
		// 公有
		this.id = id;
		// 特权方法
		this.getName = function(){};
		this.setName = function(){};
		this.getPrice = function(){};
		this.setPrice = function(){};
	}
	_book.prototype = {
		// 静态公有
		isJSBook: true,
		display: function(){}
	};
	return _book;
})();
```
	ps: 静态私有变量只存在一份，如果通过创建类中的特权方法修改静态私有变量，所有对象实例中的此静态私有变量都会改变。(闭包)
	在6.中通过特权方法修改私有变量，其他对象中的私有变量，不会改变。
	这个，怎么理解呢？回头还需要研究下。

### 8.创建对象的安全模式
在创建实例对象的时候，如果没有使用new操作符，会造成this指向的是全局环境，在浏览器中也就是window对象。为了避免这种情况，可以使用instanceof来检测。
```javascript
var Book = function(id, name, price) {
	if(this instanceof Book) {
		this.id = id;
		this.name = name;
		this.price = price;
	} else  {
		return new Book(id, name, price);
	}
}
```
----------------
　　　　-------来自 张容铭《Javascript设计模式》 和 阮一峰 《Javascript面向对象编程》感谢