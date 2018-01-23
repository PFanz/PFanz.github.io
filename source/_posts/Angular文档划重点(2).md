---
title: Angular文档划重点(2)
date: 2018-01-23
tags: [Angular]
categories:
- JavaScript
comments: true
---

### 表单

#### 用户输入

* 绑定用户输入事件：(click) => "toClick()"

* 通过$event获取事件，尽量传递值，而不是事件

* 从模板引用变量获取用户输入的值： #var 必须绑定事件（即使是空事件(keyup)="0"）

#### 模板驱动表单

* 使用绑定语法[(ngModel)]=""，并且必须有name属性

* 通过在form标签上添加#var="ngForm"，可以引用form表单，提供var.from.valid等属性

* 需要引入FormsModule模块

* ngModel会跟踪修改状态与有效性验证，包括添加类名ng-touched与ng-untouched、ng-dirty与ng-pristine、ng-valid与ng-invalid

* 通过#var="ngModel"可以获得元素，并且可以访问touched、untouched、dirty等属性

* 使用ngSubmit提交表单

#### 模型驱动表单（响应式表单）

* 属性关联到模板： 模板中[formControl]="var"，类中var = new FormControl()

* 导入ReactiveFormsModule

* AbstractControl是三个具体表单类的抽象基类。并为它们提供了一些共同的行为和属性，其中有些是可观察对象

* FormControl用于跟踪一个单独的表单控件的值和有效性状态。它对应于一个HTML表单控件，比如输入框和下拉框

* FormGroup用于跟踪一组AbstractControl的实例的值和有效性状态。该组的属性中包含了它的子控件。

* FormArray用于跟踪AbstractControl实例组成的有序数组的值和有效性状态

* FormGroup使用： 模板中[formGroup]="heroForm",[formControl]="name"需要修改为formControlName="name",类中heroForm = new FormGroup({name: new FormControl()})。novalidate可以阻止浏览器默认表单校验

* FormBuilder可以减少FormControl的编写： 明确类型var: FormGroup；构造函数中传入FormBuilder: constructor(private fb: FormBuilder)；创建表单this.var = this.fb.group({name: '', // <--- the FormControl called "name"})

* Validators验证器：上例中this.var = this.fb.group({name: ['', Validators.required]})

* 多级FormGroup： 同样将[formGroup]="var"改为formGroupName="var"

* 通过formGroup.get('var')可以获得单个formControl状态：主要包括value、status、pristine、untouched等

#### 表单验证

* 模板驱动表单中，通过#var="ngModel"可以将NgModel导出为变量var。

* 响应式表单中，实例化FormControl时第二个参数可以传入同步验证器，第三个参数可以传入异步验证器。
