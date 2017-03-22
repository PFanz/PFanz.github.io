---
title: 从零开始搭建React(3)
date: 2016-11-16
tags: [JavaScript, React, Webpack]
categories: 
- React
comments: true
---

相信学习React的都是有过一定了解的，React页面都是一个个组件构成的，所以写React也就是写一个个组件的过程。  
目前，从写法上来说React组件主要有两种写法，一种是函数式(Functional)组件，一种是继承类(Class)组件：
```javascript
// Functional
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>
}
// ES6下更多情况会写成这样，其实就是一段返回一段jsx的函数
const Welcome = (props) => {
  return (
    <h1>Hello, {props.name}</h1>
  )
}
export default Welcome

// Class
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
// ES6下一般为方便导入加入下面
export default Welcom
```
按照React组件的功能可以分为有状态组件和无状态组件。无状态组件一般使用函数式组件即可。  
我的建议是，如果不清楚是用哪一种写法去编写组件，可以先使用函数式组件。在编写过程中如若发现需要使用state或者组件生命周期，再将此组件改成继承类组件。下面是一个比较完整的继承类组件架子：
```javascript
class MyComponent extends Component {
    // 构造函数
  constructor (props) {
    super(props)
    // 设置state
    this.state = {
      ...
    }
    // 定义 eventHandler
    this.handleClick = this.handleClick.bind(this)
  }

  // 生命周期方法
  componentWillMount () {}
  componentDidMount () {}
  componentWillReceiveProps () {}
  componentWillUpdate () {}
  componentWillUnmount () {}

  // handlers
  handleClick() {
    this.setState({
      ...
    })
  }

  render() {
    // 这里是组件的内容
    return (
        <div onClick={this.handleClick}>
        </div>
      )
  }
}

MyComponent.defaultProps = {}
MyComponent.propTypes = {}

export default MyComponent
```
    需要注意点：
      无论哪种写法，return中必须被一个标签包裹，这是jsx语法要求的。
      函数式组件也可以设置defaultProps和propTypes。
      函数式组件中不能访问this。
      可以声明变量值为jsx，如：let a = <div></div>