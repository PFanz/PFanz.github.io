---
title: 面向对象的javascript(继承)
date: 2016-06-21 20:33
tags: [javascript,面向对象]
categories: 
- javascript
comments: true
---
　　继承就是从已有的类(对象)派生出新的类(对象)，新的类(对象)会拥有父类的所有属性和方法，并可以添加或修改属性和方法。

--------------------

### 1.类式继承(prototype模式)
```javascript
// 父类
function SupClass() {
	this.superValue = true;
}
SuperClass.prototype = {
	getSuperValue = function() {
		return this.superValue;
	}
};
// 子类
function SubClass() {
	this.subValue = false;
}
// 通过原型链实现继承
SubClass.prototype = new SuperClass();
// 修正constructor的指向
SubClass.prototype.contructor = SubClass;
// 子类添加公有方法
SubClass.prototype.getSubValue = function() {}
```
将父类实例赋值给子类的prototype，子类就能通过原型链来访问到父类中的属性以及方法。缺点是因为子类是通过原型链继承父类的，所以多个实例化的子类中包含的父类属性和方法其实是一份，其中一个修改，其他会受到影响。另外是实例化无法传递参数。

### 2.构造函数继承
```javascript
// 父类
function SupClass(id) {
	this.superValue = true;
	this.id = id;
}
SuperClass.prototype = {
	getSuperValue = function() {
		return this.superValue;
	}
};
// 子类
function SubClass(id, name) {
	SupClass.call(this, id);
	this.name = name;
}
```
通过call或apply函数修改父类中的this指向，可以实现参数的传递。缺点是只能继承构造函数中的属性(即this指向的属性和方法)，原型链得不到继承。

### 3.组合继承
```javascript
// 父类
function SupClass(id) {
	this.superValue = true;
	this.id = id;
}
SuperClass.prototype = {
	getSuperValue = function() {
		return this.superValue;
	}
};
// 子类
function SubClass(id, name) {
	// 继承构造函数中的属性和方法
	SupClass.call(this, id);
	this.name = name;
}
// 继承所有属性和方法
SubClass.prototype = new SuperClass();
SubClass.prototype.contructor = SubClass;
```
结合了类式继承和构造函数继承的优点，去除了两者的缺点。唯一的缺点就是在子类中执行了SupClass()，在子类原型中又执行了SupClass()。

### 4.直接继承prototype
```javascript
// 同上
... ...
// 继承原型中的属性和方法
SubClass.prototype = SuperClass.prototype;
SubClass.prototype.contructor = SubClass;
```
既然在子类中构造函数的属性和方法已经被继承了，所以子类的原型中只需要继承父类的原型。缺点是SubClass.prototype和SupClass.prototype指向同一对象，修改会互相影响。

### 5.利用空对象继承prototype
```javascript
// 同上
... ...
// 在空对象中复制一份父类的原型
var O = function(){};
O.prototype = SuperClass.prototype;
// 继承原型中的属性和方法
SubClass.prototype = new O();
SubClass.prototype.contructor = SubClass;
```
基于3.的修改，执行两次SupClass()消耗资源，执行空对象，消耗资源就会大大减少。

### 6.拷贝继承
拷贝就是将父类的属性和方法拷贝到子类中，拷贝可以是浅拷贝和深拷贝。拷贝可以实现多继承。

----------------
　　　　-------来自 张容铭《Javascript设计模式》 和 阮一峰 《Javascript面向对象编程》感谢