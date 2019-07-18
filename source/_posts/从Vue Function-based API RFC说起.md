---
title: 从Vue Function-based API RFC说起
date: 2019-07-11
tags: [JavaScript, Vue, React]
categories:
- Vue
comments: true
---

最近尤雨溪尤大在知乎上发布了《Vue Function-based API RFC》，Function-based API是受React Hooks的启发引入的逻辑复用解决方案，但是这个变动引起了不小的争议。

先看下文章中的示例，了解下Vue3.0和Vue2.0代码上的变化 <!-- more -->

```javascript
import { value, computed, watch, onMounted } from 'vue'

const App = {
  template: `
    <div>
      <span>count is {{ count }}</span>
      <span>plusOne is {{ plusOne }}</span>
      <button @click="increment">count++</button>
    </div>
  `,
  setup() {
    // reactive state
    const count = value(0)
    // computed state
    const plusOne = computed(() => count.value + 1)
    // method
    const increment = () => { count.value++ }
    // watch
    watch(() => count.value * 2, val => {
      console.log(`count * 2 is ${val}`)
    })
    // lifecycle
    onMounted(() => {
      console.log(`mounted`)
    })
    // expose bindings on render context
    return {
      count,
      plusOne,
      increment
    }
  }
}
```

第一眼看上去，变化最大的有两点：

1. App被直接赋值为了一个对象，而2.0中是基于 Class 创建的；
2. 对象中有一个`setup`，`setup`中调用的从`vue`中导出的`computed`、`onMounted`等方法，最后返回了模板中使用的`count`、`plusOne`变量和`increment`方法。

这种设计就是标题中的`Function-based API`，动机是提升逻辑组合与复用、类型推导、打包尺寸这三个方面。  
文章中提到这种设计是受`React Hook`的启发，既然这种方式是受`React Hook`的启发，我们可以先来了解下`React Hook`。

## React Hook

React 中，组件的编写方式有两种：`function`和`class`。组件根据是否包含state，分为有状态组件和无状态组件。  
在React Hook之前，有状态组件只能通过class的方式编写，而因为`function`编写组件性能更好，无状态组件推荐使用纯函数编写。

Hook是React 16.8的新增特性，它可以让你在不编写class的情况下使用state 以及其他的 React 特性。从官方这句描述中，我们可以看出，Hook关键就是不用编写class。

#### class 难以理解

1. JavaScript中的`this`是令人难以理解的，而使用 class 需要你对`this`有深入的理解。

```javascript
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // 为了在回调中使用 `this`，这个绑定是必不可少的
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
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

```javascript
class LoggingButton extends React.Component {
  // 此语法确保 `handleClick` 内的 `this` 已被绑定。
  // 注意: 这是 *实验性* 语法。
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

2. 有状态组件和无状态组件需要提前区分，以确定编写时使用`function`还是`class`。

3. `class`不能很好的压缩，难以优化等。（这方面偏`React`库的工作，提到了`Svelte`、`Angular`等库使用的预编译能力）

在有了`React Hook`之后，我们就不需要编写`class`组件，也就能够避免上面的问题。

```javascript
// useState可以创建状态
import React, { useState, useEffect } from 'react';
...
```

#### 复杂组件难以理解

这里主要提到的问题是按照组件的生命周期进行逻辑上的拆分。很多情况下，我们需要在`DidMount`和`DidUpdate`中做同样的获取数据操作，在`DidMount`中又需要做事件监听等操作而在`WillUnmount`做对事件监听做清除操作。我们发现在`DidMount`和`DidUpdate`中包含一些相同的操作而没能很好的复用，许多相互之间无关的操作又混杂在同一个生命周期中，订阅和取消订阅这种相关联的操作被分隔在两个生命周期中使得需要全局变量来标记。最终，就会导致复杂组件越来越难以理解。  
`React Hook`将组件中相互关联的部分拆分成更小的函数。

```javascript
// useEffect可以使用生命周期
import React, { useState, useEffect } from 'react';
...
```

#### 组件间复用状态逻辑困难

举个例子，比如最近在写的假面派对项目中，聊天列表页和聊天页都会有倒计时的功能，倒计时功能包括页面上倒计时的展示和倒计时结束后的一系列变化。我们需要做的工作包括：在组件挂载时请求接口，计算出初始的倒计时时间；使用定时器计算新的倒计时时间；组件销毁时，删除定时器。  
我们希望是可以在不同页面中复用这个工作，在`React`中，社区给出的解决方案有`render props`和`高阶组件`，这两个方案都是通过嵌套来解决的，这会带来容易形成嵌套地狱、需要重新组织组件结构、容易产生命名冲突等问题。  
`React Hook`可以像普通方法一样，通过`import`导入后直接调用。

```javascript
import React, { useState, useEffect } from 'react';

// 通过自定义Hook，就可以实现逻辑的复用
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```

    React Hook 列表
    
        基础Hook
            - useState（创建state）
            - useEffect（生命周期）
            - useContext（使用上下文）
        额外的Hook
            - useReducer（创建reducer，类似redux中的reducer）
            - useCallback（创建计算属性，类似vue中的computed）
            - useMemo（同上，参数不同）
            - useRef（ref）
            - useImperativeHandle
            - useLayoutEffect
            - useDebugValue

## Vue Function-based API

在看过`React Hook`之后，会发现`Vue Function-based API`确实有很多借鉴。  

除了上面`React Hook`中提到的几个设计目的外，文章中还提到了类型推导这个目的。  
文章中说：“3.0 的一个主要设计目标是增强对 TypeScript 的支持。原本我们期望通过Class API来达成这个目标，但是经过讨论和原型开发，我们认为 Class 并不是解决这个问题的正确路线，基于 Class 的 API 依然存在类型问题。”

#### 不同

1. React直接使用`function`，Vue是对象中的`setup`，所以React中完全不需要考虑`this`，而Vue中的`this`是可以用的。

2. React中通过`return` `JSX`作为渲染模板，Vue是对象中的`template`属性，所以React中的`JSX`可以使用`function`中的所有变量，Vue的`template`中只能使用`setup`的返回（类似data）。

其他还有很多细节上的不同，这里就不再继续罗列了。

## 升级策略

最后提一下文章中提到的升级策略，提供两个版本：兼容版本和标准版本。虽然提供两个版本，但是文章也提到——“2.x 的用户可以从兼容版本开始逐步地减少对旧选项的使用，直到最终切换到标准版本。”所以，还是应该积极拥抱新版本。  

附上原文[链接](https://zhuanlan.zhihu.com/p/68477600)
