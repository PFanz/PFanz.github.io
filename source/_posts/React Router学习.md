---
title: React Router学习
date: 2016-08-24
tags: [JavaScript, React]
categories: 
- JavaScript
comments: true
---

### 0. 路由

路由也就是地址，就是定义不同url访问不同页面的功能，可以实现导航、页面切换/跳转等功能。

### 1. 引入
```javascript 
import { Router, Route, hashHistory } from 'react-router'
```

### 2. 配置
```javascript
<Router history={browserHistory}>
	<Route path="/" component={Home}></Route>
	<Route path="article" component={Article}></Route>
</Router>
```

### 3. 通过Link实现跳转
```javascript
<Link to="/about">About</Link>
```

### 4.1 路由嵌套(公用导航)
```javascript
// 路由配置
<Router history={hashHistory}>
  <Route path="/" component={App}>
    <Route path="/repos" component={Repos}/>
    <Route path="/about" component={About}/>
  </Route>
</Router>
// 页面配置(通过this.props.children调用)
{this.props.children}
```

### 4.2 Active Links
```javascript
// 活动样式
<Link to="/about" activeStyle={{ color: 'red' }}>About</Link>
// 或者 活动类名
<li><Link to="/about" activeClassName="active">About</Link></li>
```

### 5. 传参
```javascript
// 配置
// :userName表示匹配参数
<Route path="/repos/:userName/:repoName" component={Repo}/>
// 跳转地址
<Link to="/repos/reactjs/react-router">React Router</Link>
// 页面中访问参数
{this.props.params.repoName}
```

### 6. 嵌套路由下的默认页
```javascript
// 需要引入react-router下的{IndexRoute}
<Router history={hashHistory}>
  <Route path="/" component={App}>

    {/* add it here, as a child of `/` */}
    <IndexRoute component={Home}/>

    <Route path="/repos" component={Repos}>
      <Route path="/repos/:userName/:repoName" component={Repo}/>
    </Route>
    <Route path="/about" component={About}/>
  </Route>
</Router>
```

### 7. 首页(默认页)路由地址
```javascript
// 直接使用Link to="/"，会导致该链接总是active状态
<Link to="/"></Link>
// 引入react-router下的{IndexLink}
<IndexLink to="/"></IndexLink>
// 还可以使用属性 onlyActiveOnIndex
<Link to="/" activeClassName="active" onlyActiveOnIndex={true}>Home</Link>
```
### 8. 更好的地址browserHistory
```javascript
// 引入
import { browserHistory } from 'react-router'
// 配置
<Router history={browserHistory}>
  {/* ... */}
</Router>
// 其他方面没有变化
```

### 9. 路由跳转
```javascript
// 使用browserHistory
browserHistory.push(path)
// 或者使用上下文 context
	// 定义对象
	contextType: {
		router: React.PropTypes.object
	},
	// 调用跳转
	this.context.router.push(path)
```

### 10. 路由钩子
每个路由都有Enter和Leave钩子，触发onEnter和onLeave。

ps:   
　　[阮一峰老师](http://www.ruanyifeng.com/blog/2016/05/react_router.html?utm_source=tool.lu)  
　　[github](https://github.com/reactjs/react-router)