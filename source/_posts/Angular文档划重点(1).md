---
title: Angular文档划重点(1)
date: 2018-01-23
tags: [Angular]
categories:
- JavaScript
comments: true
---

### 模板与数据绑定

#### 模板语法

* 模板中的HTML： 禁用了script、HTML、body、base等标签

* 插值表达式：{% raw %}{{}}{% endraw %}，括号中的素材是一个模板表达式，对其中内容求值，并转换为string

* 模板表达式：可能引起副作用的表达式被禁用：赋值（=, +=, -=, ...），new 运算符，使用;或,的链式表达式，自增或自减操作符 (++和--)。并且不支持位运算|和&。

* 表达式上下文：包括所在组件的对象，模板输入变量（比如：ngFor中的let item）和模板引用变量（#input）。不能引用全局变量（window.*,document.*等）。

* 模板语句：主要用于触发事件（(event)="statement"），和模板表达式不同的是支持基本赋值（=）和表达式链（;和,）。

* 语句上下文：包括所在组件的对象，模板输入变量（比如：ngFor中的let item）和模板引用变量（#input）。不能引用全局变量（window.*,document.*等）。

* 属性绑定：[]=

* attribute绑定：当元素没有属性可绑定的时候，需要使用[attr.*]=""进行绑定

* CSS类绑定：<div class="bad curly" [class]="badCurly"></div>会计算badCurly的值，赋值并覆盖之前的class。可以使用[class.*]=""来添加新的class

* 样式绑定：[style.color]=""

* 绑定事件：(click)="func()"和on-click="func()" $event原生event对象

* 使用EventEmitter实现自定义事件：new EventEmitter().emit(参数)

* 双向数据绑定：[()]双向数据绑定实际上是属性绑定和事件绑定的语法糖

* 内置结构指令：NgIf,NgFor,NgSwitch等等

* 模板引用变量：(#var) 作用域为整个模板

* 输入和输出：@Input和@Output，或者通过@Component中的元数据inputs:[]和outputs:[]

* 模板表达式操作符：管道(|)，安全导航操作符(?.)，非空断言(!)。非空断言和安全导航操作符不同的是，非空断言只是“断言”来避免TypeScript的检查，但是允许返回null和undefined

#### 生命周期钩子

|钩子|目的和时机|
|---|--------|
|ngOnChanges()|当Angular（重新）设置数据绑定输入属性时响应。 该方法接受当前和上一属性值的SimpleChanges对象当被绑定的输入属性的值发生变化时调用，首次调用一定会发生在ngOnInit()之前。|
|ngOnInit()|在Angular第一次显示数据绑定和设置指令/组件的输入属性之后，初始化指令/组件。在第一轮ngOnChanges()完成之后调用，只调用一次。|
|ngDoCheck()|检测，并在发生Angular无法或不愿意自己检测的变化时作出反应。在每个Angular变更检测周期中调用，ngOnChanges()和ngOnInit()之后。|
|ngAfterContentInit()|当把内容投影进组件之后调用。第一次ngDoCheck()之后调用，只调用一次。只适用于组件。|
|ngAfterContentChecked()|每次完成被投影组件内容的变更检测之后调用。ngAfterContentInit()和每次ngDoCheck()之后调用只适合组件。|
|ngAfterViewInit()|初始化完组件视图及其子视图之后调用。第一次ngAfterContentChecked()之后调用，只调用一次。只适合组件。|
|ngAfterViewChecked()|每次做完组件视图和子视图的变更检测之后调用。ngAfterViewInit()和每次ngAfterContentChecked()之后调用。只适合组件。|
|ngOnDestroy()|当Angular每次销毁指令/组件之前调用并清扫。 在这儿反订阅可观察对象和分离事件处理器，以防内存泄漏。在Angular销毁指令/组件之前调用。|

#### 组件交互

* 通过输入型绑定把数据从父组件传到子组件：@Input

* 通过setter截听输入属性值的变化

* 通过ngOnChanges()来截听输入属性值的变化

* 父组件监听子组件的事件： 通过@Output一个EventEmitter对象

* 父子组件通过本地变量交互： #var

* 父组件调用@ViewChild()： AfterViewInit钩子中

* 通过服务通讯

#### 组件样式

* 组件的样式有三种封装模式：原生(Native)、仿真(Emulated)、无(none)。原生会通过Shadow DOM保证样式不影响组件外其他元素，仿真会通过改名等特殊处理实现不影响外面其他元素。

#### 动态组件

* 通过componentFactoryResolver下的方法(如：resolveComponentFactory)解析出一个ComponentFactory对象。

* 通过ViewContainerRef可以获取对容器视图的访问权(包括clear、createComponent等方法)。

* 将ComponentFactory传递给ViewContainerRef的createComponent即可将组件动态添加到ViewContainerRef中。

#### 属性型指令

* Directive提供@Directive装饰器功能。

* 通过ElementRef可以访问DOM元素。

* 通过@Input传入参数

* 通过@HostListener添加响应事件

#### 结构型指令

* *号是一个微语法，解开会变成<ng-template>标签。

* <ng-template>会被转换成注释，不会显示出来。

* <ng-container>不会被渲染的容器。

* 通过TemplateRef取得<ng-template>的内容，通过VeiwContainerRef来访问视图容器

#### 管道

* 自定义管道需要实现PipeTransform接口的transform方法，接受一个输入值和参数

* 纯管道：只有发生纯变更（原始类型变更，或引用地址的变更）的时候才会执行纯管道。

* 非纯管道：元数据中添加pure: false。

* 非纯异步管道：接受一个Promise或Observable作为输入，最终返回给出的值。

#### 动画

* 元数据中定义animations

* 定义不同状态下的样式，定义状态砖厂动画效果。

* *为任意状态，void状态为未进场或移除状态。
