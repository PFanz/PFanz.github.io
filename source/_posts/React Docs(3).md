---
title: React Docs（3）
date: 2017-01-16
tags: [JavaScript, React]
categories: 
- React
comments: true
---

## 列表和键（key）
在React中转换数组和在JavaScript中几乎相同，可以通过数组的map方法渲染多个组件：
```javascript
// 显示内容为1到5的列表
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```
在运行此代码的时候，会收到一条警告，警告指出需要为每一项提供一个key，这个是很重要的。
```javascript
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```
### 键（key）
key可以帮助确定哪些项发生了变化，或者添加、删除了哪些项，通常使用数据的id作为key，这样在整个列表中key的值是唯一的。
key只有在周围环境是数据的情况下才有意义，如下：
```javascript
// 错误的做法
function ListItem(props) {
  const value = props.value;
  return (
    // 这里不必要使用key，使用
    <li key={value.toString()}>
      {value}
    </li>
  );
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // key属性应该添加在这里
    <ListItem value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```
其实只需要记得在map函数中使用key就可以了。

### key在兄弟节点间是唯一的
数组中使用key需要是唯一的，但是在全局不需要是唯一的。当生成两个不同的数组，我们可以使用相同的键。  
key作为组件的标识，但是不会将值传递给组件，如果需要使用key中的值，需要另外传给组件的一个属性。  
JSX中可以嵌套map函数。这种形式有时候会使代码更加清晰，有时候也会被滥用，这就需要你来决定是否需要提取出来，以提高可读性。

## 表单
表单元素自身本来就拥有一些属性（比如name等），表单在用户提交时，会执行默认的表单行为，提交到一个新的页面。但是在多数情况下，我们使用JavaScript函数处理表单的提交，处理用户提交的数据。实现这种方法的组件我们称为“受控组件”。

### 受控组件
在HTML中，表单元素如`<input>`，`<textarea>`和`<select>`通常保持自己的状态，并根据用户的输入进行更新。而在React中，状态一般保存都state属性中，并且state属性只能通过`setState()`更新。  
我们可以将React的state设置成HTML的输入将两者结合，然后React组件还需要控制用户的输入，这样的组件被称为“受控组件”。
```javascript
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```
其中将input元素的value值交给React组件的state，通过handleChange函数响应每次输入来更新state。使用受控组件，每个输入都会关联到处理函数，这使得可以直接修改或验证用户输入。

### textarea标签
在React中，textarea标签也使用了value属性来代替在元素中的显示值。

### select标签
在HTML中使用`<select>`标签时候，可以使用option中selected属性表示当前选择的选项。而在React中，将当前选项保存在select中的value，当select中的value与option的Value相同时，该选项被选择。

### 受控组件的代码
使用受控组件有时候是很复杂的，因为你需要为更改数据的所有方式编写事件处理函数，并通过React组件管理所有输入状态。这时候可能需要不受控组件。

## 提升state属性
通常，几个组件需要反映相同的数据变化，这时候可以将共享的state提升到最接近的共同的父组件中。

下面这个组件BoilingVerdict接受一个celsius温度参数，并打印是否能把水烧开
```javascript
function BoilingVerdict(props) {
  if (props.celsius >= 100) {
    return <p>The water would boil.</p>;
  }
  return <p>The water would not boil.</p>;
}
```
下面这个组件Calculator，输入一个celsius摄氏温度，保存到this.state.value中，并作为BoilingVerdict输入。
```javascript
class Calculator extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {value: ''};
  }

  handleChange(e) {
    this.setState({value: e.target.value});
  }

  render() {
    const value = this.state.value;
    return (
      <fieldset>
        <legend>Enter temperature in Celsius:</legend>
        <input
          value={value}
          onChange={this.handleChange} />
        <BoilingVerdict
          celsius={parseFloat(value)} />
      </fieldset>
    );
  }
}
```

### 添加第二个输入
输入一个华氏温度，能够和摄氏度相互转换。  
首先定义两个函数实现华氏度和摄氏度转换，再定义一个函数实现将值转换为字符串：
```javascript
function toCelsius(fahrenheit) {
  return (fahrenheit - 32) * 5 / 9;
}

function toFahrenheit(celsius) {
  return (celsius * 9 / 5) + 32;
}

function tryConvert(value, convert) {
  const input = parseFloat(value);
  if (Number.isNaN(input)) {
    return '';
  }
  const output = convert(input);
  const rounded = Math.round(output * 1000) / 1000;
  return rounded.toString();
}
```
下面是温度输入组件
```javascript
class TemperatureInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
  }

  handleChange(e) {
    this.props.onChange(e.target.value);
  }

  render() {
    const value = this.props.value;
    const scale = this.props.scale;
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}:</legend>
        <input value={value}
               onChange={this.handleChange} />
      </fieldset>
    );
  }
}
```
下面是温度转换，以及显示沸水情况组件
```javascript
class Calculator extends React.Component {
  constructor(props) {
    super(props);
    this.handleCelsiusChange = this.handleCelsiusChange.bind(this);
    this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this);
    this.state = {value: '', scale: 'c'};
  }

  handleCelsiusChange(value) {
    this.setState({scale: 'c', value});
  }

  handleFahrenheitChange(value) {
    this.setState({scale: 'f', value});
  }

  render() {
    const scale = this.state.scale;
    const value = this.state.value;
    const celsius = scale === 'f' ? tryConvert(value, toCelsius) : value;
    const fahrenheit = scale === 'c' ? tryConvert(value, toFahrenheit) : value;

    return (
      <div>
        <TemperatureInput
          scale="c"
          value={celsius}
          onChange={this.handleCelsiusChange} />
        <TemperatureInput
          scale="f"
          value={fahrenheit}
          onChange={this.handleFahrenheitChange} />
        <BoilingVerdict
          celsius={parseFloat(celsius)} />
      </div>
    );
  }
}
```
### 总结
对于React中的任何数据，应该有一个单一的来源。通常将state添加到需要渲染的组件，如果其他组件也需要该state，将它提升到最接近的共同父组件中，而不是试图同步不同组件之间的state。这种解决方法就是自上而下的数据流。  
提升state需要编写更多的代码，但是这可以方便寻找和修改bug。state存在一些组件中，在这些组件中都可以单独改变该state，这可以减少错误。另外，你可以控制用户的输入。  
如果某个state属性，可以通过props或者其他state属性得到，我们可能就不需要该state属性。

## 组件的组成和继承
React组件具有强大的组合功能，我们建议使用组合来重用组件之间的代码，而不是继承。
### 包含
一些组件提前不知道自己包含什么。可以使用props的children来传递包含的组件
```javascript
// 使用props.children来调用被包含的组件
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}
// 其他组件通过JSX传递被包含的组件给父组件
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  );
}
```
有时候也可以包含多个子组件，可以通过自定义props传递
```javascript
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  );
}
// 向SplitPane组件中传递Contacts和Chat组件
function App() {
  return (
    <SplitPane
      left={
        <Contacts />
      }
      right={
        <Chat />
      } />
  );
}
```
### 特殊化
有些组件是另一些组件的特殊情况。比如：WelcomDialog是Dialog的特例。可以通过传递props来实现
```javascript
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
    </FancyBorder>
  );
}

function WelcomeDialog() {
  return (
    <Dialog
      title="Welcome"
      message="Thank you for visiting our spacecraft!" />
  );
}
```
### 不建议使用继承
props和组合可以使React组件有很大的灵活性，组件可以接受任意props，原始值，React组件，以及函数。  
如果组件之间重用非UI功能，建议可以将这部分函数单独提到一个JavaScript模块中，组件可以导入该模块中的函数、对象或者类，而不需要继承。

## Think in React
### 第一步 拆分组件
拆分组件应该遵循单一职责原则，每个组件只负责做一件事。一般情况下，正确的组件划分能够正好的映射JSON数据模型。[具体参考这里](https://facebook.github.io/react/docs/thinking-in-react.html)
### 第二步 构建静态版本
根据层次解构组合组件，接受模拟的数据呈现出UI，但是这是没有交互性的。  
构建静态版本，要构建一个重用其他组件的组件，并且使用props传递数据。在静态版本中，不要使用state。state保留用于交互，所以这里不需要使用它。  
在构造过程中，可以自上到下，也可以使用自下到上。  
在完成这一步后，你将有一个可重用的组件库，用来呈现你的模拟数据。组件只会有render()方法。在最上层组件，传递模拟数据，更改模拟数据查看组件的变化是否正常。
### 第三步 识别最小且完整的state
正确的构建项目，首先需要考虑项目所需要考虑所有需要的最小的state集合，并且通过这些state计算出所有需要的其他内容。如果是能够计算出来的，就不需要存储在state中。  
比如在一个TODO列表中，所有数据包括：
* 原本的列表
* 用户输入搜索框的内容
* 用户勾选的选项
* 过滤掉的列表
对于这样的所有数据，我们需要弄清哪些才是state，可以问自己三个问题：
1. 它可以通过父组件的props传递进来吗？
2. 是否不会变化？
3. 是否可以通过其他state或props计算出来？
如果有一个回答是，那么该属性不应该是state。  
原本的列表应该是通过props传递来的，过滤掉的列表可以通过原本的列表和输入框中的值计算出来。最后得到的state是：
* 用户输入搜索框的内容
* 用户勾选的选项
### 第四步 确定state应该在哪个位置
确定好state之后，是要确定在哪里定义state，在哪里更改state。  
对于每一个state：
* 确定哪些组件要用到该state
* 找到一个包含这些所有组件的父组件
* 包含这些所有组件的父组件其中结构层次最高的组件应该拥有该state
* 如果找不到这样的父组件，创建一个这样的父组件，并且添加state
### 第五步 添加反向数据流
因为是单向数据流，所以需要添加操作对state的更改。比如，输入框绑定了用户输入搜索框内容这个state，输入框就不会响应用户的输入行为，需要为input绑定修改state事件。这里是子组件修改父组件中的state，所以就是反向数据流。