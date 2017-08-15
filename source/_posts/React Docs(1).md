---
title: React Docs（1）
date: 2016-12-09
tags: [JavaScript, React]
categories: 
- React
comments: true
---

### 安装
React在codepen上提供了一个Hello，World项目事例，只需打开[网站](http://codepen.io/gaearon/pen/rrpgNB?editors=0010)，即可尝试React。  
另外还提供了一个[html文件](https://facebook.github.io/react/downloads/single-file-example.html)的Hello，World项目，项目中引用CDN的react.js、react-dom.js以及用于编译babel的babel.min.js，运行比较缓慢，只适合学习语法使用。

### 创建单页面应用
创建React应用的最好方法是单页面应用，这里是最好方法，也是可以创建多页面应用的。可以通过官方方法去创建你的应用：
```shell
npm install -g create-react-app
create-create-app hello-world
cd hello-world
npm start
```
这里不涉及任何后端逻辑和数据库，可以使用任何你想用的后端。create-react-app中使用了Webpack，Babel和ESLint，但是你可以自己去配置他们。

#### 将React加入一个现有的应用
使用`npm install --save react react-dom`安装React。  
将其导入你的代码中，如：
```javascript
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
)
```
这段代码应该被包含在一个拥有id为root的HTML元素，所以你的HTML文件需要`<div id="root"></div>`  
当你使用上面方法使用React的时候，需要使用预设为es2015和react的Babel进行转化代码。使用React在生产环境的时候，设置NODE_ENV为"production"。

#### 使用ES6和JSX
建议是用Babel将你的ES6和JSX代码转换为JavaScript代码，Babel可以有很多不同的配置，但是确保里面包含`babel-preset-react`和`babel-preset-es2015`。

#### 使用CDN
如果不想使用npm，可以使用CDN
```html
<script src="https://unpkg.com/react@15/dist/react.js"></script>
<script src="https://unpkg.com/react-dom@15/dist/react-dom.js"></script>
```
@后面数字可以指定版本
```html
<script src="https://unpkg.com/react@15/dist/react.min.js"></script>
<script src="https://unpkg.com/react-dom@15/dist/react-dom.min.js"></script>
```

### JSX
在JSX中，可以在任意地方插入JavaScript代码，只需要将JavaScript放在大括号中即可。下面的代码都是合法的：
```jsx
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
```
将JSX代码放在小括号中，像HTML代码一样换行，可以方便阅读。

##### JSX也是表达式
JSX最后也被编译成JavaScript代码，这就以为着JSX可以赋值给一个JavaScript变量，可以作为方法的参数，可以被方法返回。

##### JSX添加HTML属性
可以用字符串设置属性，也可以通过使用{}将JavaScript表达式赋值给属性。

##### 闭合JSX
如果JSX是空的可以使用`/>`来闭合标签，如果包含其他标签，也可以使用</***>闭合。  
虽然JSX很接近HTML，但是还是有些不同，比如属性使用小驼峰法命名，class使用className，for使用forHtml等。

##### JSX防止注入攻击
默认情况下，React DOM在渲染JSX之前会对其进行编码。

##### JSX描述对象
Babel会将JSX转化为React.createElement()形式，比如下面这样：
```jsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
)
```
转化为
```javascript
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```
React.createElement会对你的代码进行一些检查，但是本质上就是将其转化成一个对象：
```javascript
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world'
  }
}
```

### 组件
将你的页面分成一个个组件，每个组件都是独立的、可以重复使用的。组件就像JavaScript中的方法，可以接受一些输入参数，返回React元素

##### Funtional 和 Class 组件
定义组件最简单的方式是使用JavaScript方法：
```javascript
function Welcome (props) {
  return <h1>Hello, {props.name}</h1>
}
这个方法就是个有效的React组件，因为它接受props作为参数，并且返回了一个React元素。

另外也可以使用ES6中的类(class)来定义组件：
```javascript
class Welcome extends React.Component {
  render () {
    return <h1>Hello, {this.props.name}</h1>
  }
}
```
上面两种定义组件的方法是等价的。  
但是Class组件能够添加一些额外的功能，我们将在下面讨论。在不需要这些额外的方法，可以使用Functional组件。

##### 渲染组件
渲染如下：
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```
过程：
  1. 调用ReactDOM.render()方法，参数为自定义组件<Welcome name="Sara" />。
  2. React调用组件Welcome，传递`{name="Sara"}`作为参数。
  3. Welcome组件返回`<h1>Hello, Sara</h1>`。
  4. React将`<h1>Hello, Sara</h1>`更新到页面。

##### 组件组合
组件可以任意组合。  
通常情况，React应用中在最顶端是一个`App`组件。组件必须有一个根元素，也就是所有元素都得包裹起来。

##### 提取组件
不要担心组件被拆分的太小。下面是一个对组件进行拆分的例子。  
Comment组件：  
```javascript
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```
在Comment组件中，使用了`author (object)`，`text (string)`，`date (date)`来描述这个对象。这样的组件因为组合的原因是很难复用的。

第一步，提取出一个`Avatar`组件：
```javascript
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
  );
}
```
这里的`Avatar`组件我们系统他更通用，所以说用了`user`而不是`author`。  
根据组件去命名参数，而不是根据使用上下文。

下一步是提取`UserInfo`组件，这里使用了上面提取的`Avatar`组件：
```javascript
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}
```
这样，我们的`Comment`组件就成了下面的样子：
```javascript
function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

##### Props是只读的
无论是Funcional还是Class组件，都是不能够修改props的。
```javascript
// 下面这个是纯函数，因为它没有试图改变自己的输入，并且对相同的输入会有相同的输出。
function sum(a, b) {
  return a + b;
}
// 下面这个不是纯函数，因为他改变了输入的值
function withdraw(account, amount) {
  account.total -= amount;
}
```
通过官方这个例子，可以明白纯函数有两点，一是相同的输入就会有相同的输出，第二点是不会更改输入的参数。

所有的React组件对于props参数必须像纯函数一样，也就是不会试图去修改props，并且对于相同的props会有相同的输出。  
当然，React也提供了随时间动态变化组件的方法，state允许根据用户行为、网络反馈等改变React组件的输出形式，这并不违背React纯函数原则。

### state 和 生命周期
请看下面一段代码：
```javascript
const Clock = (
  <div>
    <h1>Hello, world!</h1>
    <h2>It is {new Date().toLocaleTimeString()}.</h2>
  </div>
);
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```
这段代码只能够显示一个Hello,world!与一个生成页面的时间。如果想要时间不停的更新，就需要使用setInterval()不停的去执行。
```javascript
// 这样
function tick() {
  const Clock = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    <Clock />,
    document.getElementById('root')
  );
}
setInterval(tick, 1000);

// 或者这样
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}
setInterval(tick, 1000);

// 但是下面这种就是不可行的，原因在于Clock组件定义被转化成了一个对象，在之后没有发生过变化。
const Clock = (
  <div>
    <h1>Hello, world!</h1>
    <h2>It is {new Date().toLocaleTimeString()}.</h2>
  </div>
);
setInterval(
function(){
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
)}, 1000)
```
这样的代码的问题在于时间的变化应该是`Clock`组件的行为，而不应该定义在全局。  
`state`可以帮助我们解决这个问题，state与props类似，但是更私有且被组件所控制，而不是传递过来的。

##### 将Functional组件转换为Class组件
Functional组件是无状态组件，不能使用state，所以首先需要将Functional转化为Class组件：
  1. 创建一个和Functional组件名相同的ES6 class继承`React.Component`
  2. 添加空的方法`render()`
  3. 将Functional组件中的函数主体移动`render()`方法中
  4. 将`render()`中的`props`替换成`this.props`
  5. 删除原来的Functional组件声明
Class组件不仅可以使用state还可以使用生命周期函数。  
拿上面的`Clock`组件作为事例，经过上面的转换，现在的Clock组件应该是这个样子：
```jsx
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

##### 为Class组件添加本地state
还是上面的`Clock`组件：
  1. 将`render()`中的`this.props`替换成`this.state`
  2. 添加class构造函数(constructor)初始化this.state (Class组件总是应该将props传递给父类)
  3. 移除`date`从组件调用的props中
```javascript
class Clock extends React.Component {
  // 这里是步骤2
  constructor(props) {
    // 将props传递给父类
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```
这样只是初始化了state，后面会设置定时器。
