---
title: React Docs（2）
date: 2017-01-15
tags: [JavaScript, React]
categories: 
- React
comments: true
---

### 事件
React对事件的处理和在DOM元素上添加事件属性绑定事件的方式几乎一样，不同点在于React使用驼峰命名，而不是全小写；React中传递的是一个函数，不是一个字符串。
另一个区别是，不能使用return false来阻止事件默认行为。  
HTML中：
```HTML
<button onclick="activateLasers()">
  Activate Lasers
</button>
```
React中：
```JSX
<button onClick={activeteLasers}>
  Activate Lasers
</button>
```
在React中，event是合成事件，不需要考虑跨浏览器兼容问题。  
在使用React中，一般不应该使用addEventListener在DOM元素创建以后去绑定事件，而应该在渲染初始化的时候就提供一个监听器。  
在使用ES6语法Class创建组件时候，常见的模式是绑定事件是类中的一个方法：
```javascript
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // 这里的绑定this是必要的，这样可以使函数上下文环境为这个组件
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```
必须小心在事件回调函数中的this，在javascript中，Class中的方法默认是不绑定this的。如果忘记绑定，函调函数中的this指向的将是undefined。  
如果不想使用bind函数，可以使用箭头函数这种语法来绑定this，下面函数中handleClick属性初始化时，里面的this就被绑定到了LoggingButton中:
```javascript
class LoggingButton extends React.Component {
  // 箭头函数可以绑定this
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```
另一种方式是在定义回调的时候用箭头函数绑定this：
```javascript
lass LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // 在onClick中，用箭头函数绑定了this
    return (
      <button onClick={(e) => this.handleClick(e)}>
        Click me
      </button>
    );
  }
}
```
这种方法的问题在于每一次渲染该组件都会创建不同的回调函数。大多数情况下这是没有问题的，但是如果函调函数中传递一个prop给一个低阶组件，这些低阶组件会做额外的重复渲染。所以，一般建议使用第一种构造函数中bind绑定this或者是属性初始化中用箭头函数绑定this。

### 条件渲染
有时候需要根据组件的不同状态，渲染不同的组件。  
React中的条件渲染与JavaScript中的条件语句工作方式相同，可以使用if或者条件操作符来创建一个表示当前状态的元素，让React更新UI以配合组件状态。  
下面是根据用户的登陆状态显示不同的问候语：
```javascript
function UserGreeting(props) {
  return <h1>Welcome back!</h1>;
}

function GuestGreeting(props) {
  return <h1>Please sign up.</h1>;
}

function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}

ReactDOM.render(
  // Try changing to isLoggedIn={true}:
  <Greeting isLoggedIn={false} />,
  document.getElementById('root')
);
```
#### 元素变量
可以使用变量来存储元素，其余都不变。
```javascript
// 一个登陆按钮，一个注销按钮
function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      Login
    </button>
  );
}

function LogoutButton(props) {
  return (
    <button onClick={props.onClick}>
      Logout
    </button>
  );
}
```
下面创建一个有状态的组件LoginControl
```javascript
class LoginControl extends React.Component {
  constructor(props) {
    super(props);
    this.handleLoginClick = this.handleLoginClick.bind(this);
    this.handleLogoutClick = this.handleLogoutClick.bind(this);
    this.state = {isLoggedIn: false};
  }

  handleLoginClick() {
    this.setState({isLoggedIn: true});
  }

  handleLogoutClick() {
    this.setState({isLoggedIn: false});
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn;

    let button = null;
    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }
}

ReactDOM.render(
  <LoginControl />,
  document.getElementById('root')
);
```
除了if语句，你也可以使用更短的逻辑语句，比如&&运算符，三目运算符等。

#### 防止组件呈现
极少数的情况下才需要这么做，通过return null可以使组件不被渲染。