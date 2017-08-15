---
title: redux应用于React
date: 2016-09-22
tags: [JavaScript, React, Redux]
categories: 
- React
comments: true
---

　　不知道有多少人和我一样，redux的文档看了一遍又一遍，各种教程翻来翻去，还是不知道在自己的React项目中该如何加入redux。  
　　经过这段时间，总算有些入门的感觉，明白大致的编写流程。  
　　对于在React项目中使用redux，首先需要理解`展示型组件和容器型组件`。  
　　简单来说，展示型组件用于对展示页面，数据通过`props`获得，只有容器型组件与redux交道。一般情况下，容器型组件通过redux的`connect`函数获得state，以及action，然后调用展示型组件并通过`props`传递所需要的state和action。展示型组件需要修改state就通过容器型组件传递过来的action调用。  
eg
```jsx
// Login.js  Login组件 (此为容器组件)

// 通过connect传递state中的userData和user action
@connect(
  ({userData}) => ({userData}),
  require('ACTION/user').default
)
export default class Login extends Component {
	render() {
		return (
        <div className="login-content">
      		<div className="login">
            <p className="login-close"><Link to="user">&times;</Link></p>
            {/* 调用LoginForm组件，并把user action中的login方法通过props传递给LoginForm组件 */}
            <LoginForm
              toLogin = { this.props.login }
            >
            </LoginForm>
          </div>
        </div>
			);
	}
}
```

```jsx
// LoginForm.js  LoginForm组件 (此为展示组件)

export default class LoginForm extends Component {
  
  constructor (props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    let username = this.refs.username.value;
    let password = this.refs.password.value;
    // 对输入的处理
    if(username == '' || password == '') {
      return alert('请输入用户名和密码');
    }
    // 调用redux action来修改state
    return this.props.toLogin(username, password);
  }

  render() {
		return (
        <div className="login-form">
          <from>
            <input className="username-input" type="text" placeholder="用户名"
              ref="username"
            />
            <input className="password-input" type="password" placeholder="密码"
              ref="password"
            />
            <submit className="submit-btn" onClick={ this.handleClick }>进入头条</submit>
          </from>
        </div>
			);
	}
}
```

#####

关于action, reducer, store的编写，需要明白这个流程：

	1. dispatch()用于触发action，参数为具体action(actionCreate其实也是产生一个action)。
	2. 当有action被触发时，就会去store中查询注册的reducer(通过action.type查找),并执行该reducer返回新的state。

所以我们需要做的：

	1. 编写reducer，参数为(initstate, action)，(一般样式为switch(action.type))。
	2. 合并reducer，主要目的是可以将reducer分成多个编写，更方便管理和理解。
	3. 编写store，注册reducer到store中，这里一般都会添加redux的中间件。
	ps:
	应该是最先编写action，包括action type常量定义，可以用于action create和reducer；action create函数；action dispatch函数。函数位置具体怎么分，各有所爱吧。