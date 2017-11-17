---
layout: post
title:  "React笔记(1)"
tags:
  - JS
  - JavaScript
---


# React笔记(1)

## 事件处理

在 React 中另一个不同是你不能使用返回 false 的方式阻止默认行为。你必须明确的使用 preventDefault。例如，传统的 HTML 中阻止链接默认打开一个新页面，你可以这样写：

```
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>
```

```
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```

```
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
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

你必须谨慎对待 JSX 回调函数中的 this，类的方法默认是不会绑定 this 的。如果你忘记绑定 this.handleClick 并把它传入 onClick, 当你调用这个函数的时候 this 的值会是 undefined。





## 多个输入的解决方法

当你有处理多个受控的input元素时，你可以通过给每个元素添加一个name属性，来让处理函数根据 event.target.name的值来选择做什么。


```
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```






## 包含关系

一些组件不能提前知道它们的子组件是什么。这对于 Sidebar 或 Dialog 这类通用容器尤其常见。
我们建议这些组件使用 children 属性将子元素直接传递到输出。


```
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}

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


虽然不太常见，但有时你可能需要在组件中有多个入口，这种情况下你可以使用自己约定的属性而不是 children：

```
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
类似 <Contacts /> 和 <Chat /> 这样的 React 元素都是对象，所以你可以像任何其他元素一样传递它们。


## 特殊实例

有时我们认为组件是其他组件的特殊实例。例如，我们会说 WelcomeDialog 是 Dialog 的特殊实例。
在 React 中，这也是通过组合来实现的，通过配置属性用较特殊的组件来渲染较通用的组件。


```
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

组合对于定义为类的组件同样适用：


```
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
      {props.children}
    </FancyBorder>
  );
}

class SignUpDialog extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.handleSignUp = this.handleSignUp.bind(this);
    this.state = {login: ''};
  }

  render() {
    return (
      <Dialog title="Mars Exploration Program"
              message="How should we refer to you?">
        <input value={this.state.login}
               onChange={this.handleChange} />

        <button onClick={this.handleSignUp}>
          Sign Me Up!
        </button>
      </Dialog>
    );
  }

  handleChange(e) {
    this.setState({login: e.target.value});
  }

  handleSignUp() {
    alert(`Welcome aboard, ${this.state.login}!`);
  }
}
```





## 点表示法

你还可以使用 JSX 中的点表示法来引用 React 组件。你可以方便地从一个模块中导出许多 React 组件。例如，有一个名为 MyComponents.DataPicker 的组件，你可以直接在 JSX 中使用它：


```
import React from 'react';

const MyComponents = {
  DatePicker: function DatePicker(props) {
    return <div>Imagine a {props.color} datepicker here.</div>;
  }
}

function BlueDatePicker() {
  return <MyComponents.DatePicker color="blue" />;
}
```


## 使用 JavaScript 表达式

if 语句和 for 循环在 JavaScript 中不是表达式，因此它们不能直接在 JSX 中使用，所以你可以将它们放在周围的代码中。

```
function NumberDescriber(props) {
  let description;
  if (props.number % 2 == 0) {
    description = <strong>even</strong>;
  } else {
    description = <i>odd</i>;
  }
  return <div>{props.number} is an {description} number</div>;
}
```



## 扩展属性

如果你已经有了个 props 对象，并且想在 JSX 中传递它，你可以使用 ... 作为扩展操作符来传递整个属性对象。下面两个组件是等效的：


```
function App1() {
  return <Greeting firstName="Ben" lastName="Hector" />;
}

function App2() {
  const props = {firstName: 'Ben', lastName: 'Hector'};
  return <Greeting {...props} />;
}
```


## JavsScript 表达式

你可以将任何 {} 包裹的 JavaScript 表达式作为子代传递。例如，下面这些表达式是等价的：


```
<MyComponent>foo</MyComponent>

<MyComponent>{'foo'}</MyComponent>
```



这对于渲染任意长度的 JSX 表达式的列表很有用。例如，下面将会渲染一个 HTML 列表：

```
function Item(props) {
  return <li>{props.message}</li>;
}

function TodoList() {
  const todos = ['finish doc', 'submit pr', 'nag dan to review'];
  return (
    <ul>
      {todos.map((message) => <Item key={message} message={message} />)}
    </ul>
  );
}
```



## 函数

通常情况下，插入 JSX 中的 JavsScript 表达式将被认作字符串、React 元素或这些内容的列表。然而，props.children 可以像其它属性一样传递任何数据，而不仅仅是 React 元素。例如，如果你使用自定义组件，则可以将调用 props.children 来获得传递的子代：


```
// Calls the children callback numTimes to produce a repeated component
function Repeat(props) {
  let items = [];
  for (let i = 0; i < props.numTimes; i++) {
    items.push(props.children(i));
  }
  return <div>{items}</div>;
}

function ListOfTenThings() {
  return (
    <Repeat numTimes={10}>
      {(index) => <div key={index}>This is item {index} in the list</div>}
    </Repeat>
  );
}
```


## 布尔值、Null 和 Undefined 被忽略

false、null、undefined 和 true 都是有效的子代，但它们不会直接被渲染。下面的表达式是等价的：

值得注意的是，React 提供了一些 “falsy” 值 （即， 除了false 外，0，“”，null，undefined 和 NaN），它们依然会被渲染。例如，下面的代码不会像你预期的那样运行，因为当 props.message 为空数组时，它会打印 0：





## 为 DOM 元素添加 Ref

React 支持给任意组件添加特殊属性。ref 属性接受一个回调函数，它在组件被加载或卸载时会立即执行。
当给 HTML 元素添加 ref 属性时，ref 回调接收了底层的 DOM 元素作为参数。例如，下面的代码使用 ref 回调来存储 DOM 节点的引用。



```
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.focus = this.focus.bind(this);
  }

  focus() {
    // 直接使用原生 API 使 text 输入框获得焦点
    this.textInput.focus();
  }

  render() {
    // 使用 `ref` 的回调将 text 输入框的 DOM 节点存储到 React 
    // 实例上（比如 this.textInput）
    return (
      <div>
        <input
          type="text"
          ref={(input) => { this.textInput = input; }} />

        <input
          type="button"
          value="Focus the text input"
          onClick={this.focus}
        />
      </div>
    );
  }
}
```



## 为类组件添加 Ref

当 ref 属性用于使用 class 声明的自定义组件时，ref 的回调接收的是已经加载的 React 实例。例如，如果我们想修改 CustomTextInput 组件，实现它在加载后立即点击的效果：


```
class AutoFocusTextInput extends React.Component {
  componentDidMount() {
    this.textInput.focus();
  }

  render() {
    return (
      <CustomTextInput
        ref={(input) => { this.textInput = input; }} />

    );
  }
}
```


你不能在函数式组件上使用 ref 属性，因为它们没有实例：

但是，你可以在函数式组件内部使用 ref，只要它指向一个 DOM 元素或者 class 组件：






## 对父组件暴露 DOM 节点

在极少数情况下，你可能希望从父组件访问子节点的 DOM 节点。通常不建议这样做，因为它会破坏组件的封装，但偶尔也可用于触发焦点或测量子 DOM 节点的大小或位置。
虽然你可以向子组件添加 ref,但这不是一个理想的解决方案，因为你只能获取组件实例而不是 DOM 节点。并且，它还在函数式组件上无效。
相反，在这种情况下，我们建议在子节点上暴露一个特殊的属性。子节点将会获得一个函数属性，并将其作为 ref 属性附加到 DOM 节点。这允许父代通过中间件将 ref 回调给子代的 DOM 节点。
适用于类组件和函数式组件。



```
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    );
  }
}
```



## 非受控组件

下面的代码在非受控组件中接收单个属性。

```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.input.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" ref={(input) => this.input = input} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```


## 默认值

在 React 的生命周期中，表单元素上的 value 属性将会覆盖 DOM 中的值。使用非受控组件时，通常你希望 React 可以为其指定初始值，但不再控制后续更新。要解决这个问题，你可以指定一个 defaultValue 属性而不是 value。

同样，<input type="checkbox"> 和 <input type="radio"> 支持 defaultChecked，<select> 和 <textarea> 支持 defaultValue.






## 性能优化

如果想让组件只在props.color或者state.count的值变化时重新渲染，你可以像下面这样设定shouldComponentUpdate：

```
class CounterButton extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: 1};
  }

  shouldComponentUpdate(nextProps, nextState) {
    if (this.props.color !== nextProps.color) {
      return true;
    }
    if (this.state.count !== nextState.count) {
      return true;
    }
    return false;
  }

  render() {
    return (
      <button
        color={this.props.color}
        onClick={() => this.setState(state => ({count: state.count + 1}))}>
        Count: {this.state.count}
      </button>
    );
  }
}
```

在以上代码中，shouldComponentUpdate只检查props.color和state.count的变化。如果这些值没有变化，组件就不会更新。当你的组件变得更加复杂时，你可以使用类似的模式来做一个“浅比较”，用来比较属性和值以判定是否需要更新组件。这种模式十分常见，因此React提供了一个辅助对象来实现这个逻辑 - 继承自React.PureComponent。以下代码可以更简单的实现相同的操作：

```
class CounterButton extends React.PureComponent {
  constructor(props) {
    super(props);
    this.state = {count: 1};
  }

  render() {
    return (
      <button
        color={this.props.color}
        onClick={() => this.setState(state => ({count: state.count + 1}))}>
        Count: {this.state.count}
      </button>
    );
  }
}
```


## Portals

Portals 提供了一种很好的将子节点渲染到父组件以外的 DOM 节点的方式。



```
// These two containers are siblings in the DOM
const appRoot = document.getElementById('app-root');
const modalRoot = document.getElementById('modal-root');

class Modal extends React.Component {
  constructor(props) {
    super(props);
    this.el = document.createElement('div');
  }

  componentDidMount() {
    modalRoot.appendChild(this.el);
  }

  componentWillUnmount() {
    modalRoot.removeChild(this.el);
  }

  render() {
    return ReactDOM.createPortal(
      this.props.children,
      this.el,
    );
  }
}

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {clicks: 0};
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    // This will fire when the button in Child is clicked,
    // updating Parent's state, even though button
    // is not direct descendant in the DOM.
    this.setState(prevState => ({
      clicks: prevState.clicks + 1
    }));
  }

  render() {
    return (
      <div onClick={this.handleClick}>
        <p>Number of clicks: {this.state.clicks}</p>
        <p>
          Open up the browser DevTools
          to observe that the button
          is not a child of the div
          with the onClick handler.
        </p>
        <Modal>
          <Child />
        </Modal>
      </div>
    );
  }
}

function Child() {
  // The click event on this button will bubble up to parent,
  // because there is no 'onClick' attribute defined
  return (
    <div className="modal">
      <button>Click</button>
    </div>
  );
}

ReactDOM.render(<Parent />, appRoot);
```

## 高阶组件


具体而言，高阶组件就是一个函数，且该函数接受一个组件作为参数，并返回一个新的组件

对比组件将props属性转变成UI，高阶组件则是将一个组件转换成另一个新组件。



```
class CommentList extends React.Component {
  constructor() {
    super();
    this.handleChange = this.handleChange.bind(this);
    this.state = {
      // "DataSource" 就是全局的数据源
      comments: DataSource.getComments()
    };
  }

  componentDidMount() {
    // 添加事件处理函数订阅数据
    DataSource.addChangeListener(this.handleChange);
  }

  componentWillUnmount() {
    // 清除事件处理函数
    DataSource.removeChangeListener(this.handleChange);
  }

  handleChange() {
    // 任何时候数据发生改变就更新组件
    this.setState({
      comments: DataSource.getComments()
    });
  }

  render() {
    return (
      <div>
        {this.state.comments.map((comment) => (
          <Comment comment={comment} key={comment.id} />
        ))}
      </div>
    );
  }
}
```


```
class BlogPost extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {
      blogPost: DataSource.getBlogPost(props.id)
    };
  }

  componentDidMount() {
    DataSource.addChangeListener(this.handleChange);
  }

  componentWillUnmount() {
    DataSource.removeChangeListener(this.handleChange);
  }

  handleChange() {
    this.setState({
      blogPost: DataSource.getBlogPost(this.props.id)
    });
  }

  render() {
    return <TextBlock text={this.state.blogPost} />;
  }
}
```


```
// 函数接受一个组件参数……
function withSubscription(WrappedComponent, selectData) {
  // ……返回另一个新组件……
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      this.state = {
        data: selectData(DataSource, props)
      };
    }

    componentDidMount() {
      // ……注意订阅数据……
      DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }

    handleChange() {
      this.setState({
        data: selectData(DataSource, this.props)
      });
    }

    render() {
      // ……使用最新的数据渲染组件
      // 注意此处将已有的props属性传递给原组件
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}


const CommentListWithSubscription = withSubscription(
  CommentList,
  (DataSource) => DataSource.getComments()
);

const BlogPostWithSubscription = withSubscription(
  BlogPost,
  (DataSource, props) => DataSource.getBlogPost(props.id)
});
```



高阶组件应该使用组合技术，将input组件包含到容器组件中：

```
function logProps(WrappedComponent) {
  return class extends React.Component {
    componentWillReceiveProps(nextProps) {
      console.log('Current props: ', this.props);
      console.log('Next props: ', nextProps);
    }
    render() {
      // 用容器组件组合包裹组件且不修改包裹组件，这才是正确的打开方式。
      return <WrappedComponent {...this.props} />;
    }
  }
}
```


约定：将不相关的props属性传递给包裹组件

```
render() {
  // 过滤掉与高阶函数功能相关的props属性，
  // 不再传递
  const { extraProp, ...passThroughProps } = this.props;

  // 向包裹组件注入props属性，一般都是高阶组件的state状态
  // 或实例方法
  const injectedProp = someStateOrInstanceMethod;

  // 向包裹组件传递props属性
  return (
    <WrappedComponent
      injectedProp={injectedProp}
      {...passThroughProps}
    />
  );
}
```



## 组件生命周期

装配

这些方法会在组件实例被创建和插入DOM中时被调用：

constructor()
componentWillMount()
render()
componentDidMount()




更新

属性或状态的改变会触发一次更新。当一个组件在被重渲时，这些方法将会被调用：

componentWillReceiveProps()
shouldComponentUpdate()
componentWillUpdate()
render()
componentDidUpdate()



卸载

当一个组件被从DOM中移除时，该方法被调用：

componentWillUnmount()




其他API

每一个组件还提供了其他的API：

setState()
forceUpdate()



类属性

defaultProps
displayName

实例属性

props
state



setState()不是立刻更新组件。其可能是批处理或推迟更新。这使得在调用setState()后立刻读取this.state的一个潜在陷阱。代替地，使用componentDidUpdate或一个setState回调（setState(updater, callback)），当中的每个方法都会保证在更新被应用之后触发。若你需要基于之前的状态来设置状态，阅读下面关于updater参数的介绍。








## 部署


```
server {
  listen 8080;
  root /home/gabe/code/github/fishjar/gabe-study-notes/react/my-app/build;
  index index.html;
}

server {
  listen 4001;
  location / {
    proxy_pass http://localhost:8081;
  }
}
```

```
const express = require('express')
const path = require('path')
const port = process.env.PORT || 8081
const app = express()

// serve static assets normally
app.use(express.static(__dirname + '/build'))

// handle every other route with index.html, which will contain
// a script tag to your application's JavaScript file(s).
app.get('*', function (request, response){
  response.sendFile(path.resolve(__dirname, 'build', 'index.html'))
})

app.listen(port)
console.log("server started on port " + port)
```















