---
title: Angular文档划重点(3)
date: 2018-01-23
tags: [Angular]
categories:
- JavaScript
comments: true
---

### 启动过程

* @NgModule接受一个元数据对象，告诉Angular如何编译和启动应用。

* imports模块，declarations组件，providers服务。

* 引导AppModule有多种方式，浏览器平台主要有即时(JiT)编译和预编译器(AoT-Ahead-Of-Time)静态引导。

* 预编译器静态引导生成更小、启动更快的应用，一般用于线上环境。

* 根模块中的imports与特性模块的imports互不相干。特性模块中应该用CommonModule替换BrowserModule。

* 惰性加载：在根模块中不导入，路由中通过loadChildren:'path#ComponentName'

* 根模块调用RouterModule.forRoot，特性模块调用RouterModule.forChild

### 依赖注入

* 通过注册provider来配置注入器

* 创建服务时是用@Injectable()

* 在需要服务的构造函数中引入

### HttpClient

* 从@angular/common/http导入HttpClientModule，注入HttpClient

* 通过observe: 'response'可以获取完整的响应体

* 错误信息的类型为HttpErrorResponse，主要包括status、error、message等属性

* 通过retry()可以在发生错误时重新发送请求

* 通过参数responseType: 'text'可以获取非JSON数据

* 拦截器继承HttpInterceptor，包含intercept一个方法，参数为req: HttpRequest<any>和next: HttpHandler

* 通过providers: [{provide: HTTP_INTERCEPTORS,useClass: ClassName,multi: true}]来使用自定义的ClassName拦截器

* 拦截器处理的是比HttpClient请求更底层的事件，拦截器必须透传自己不理解的和不打算修改的事件

* HttpRequest和HttpResponse类是不可变的，需要通过clone()传递参数修改

* 通过{setHeaders: {Authorization: authHeader}设置请求头

* 通过设置reportProgress: true可以监听进度事件，主要包括event.loaded和event.total

### 路由

* 在导航时的每个生命周期成功完成时，路由器会构建出一个`ActivatedRoute`组成的树，它表示路由器的当前状态

* 需要根据参数，同一组件切换不同数据，可以使用`ParamMap`，返回`Observable`；当不需要`Observable`时，可以使用`snapshot`

* 用CanActivate来处理导航到某路由的情况；用CanActivateChild来处理导航到某子路由的情况；用CanDeactivate来处理从当前路由离开的情况；用Resolve在路由激活之前获取路由数据；用CanLoad来处理异步导航到某特性模块的情况。
