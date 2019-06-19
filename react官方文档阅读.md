## 官方起步教程 - 井字棋

###  React 的**基础知识**

组件、props 和 state

在 React 应用中，数据通过 props 的传递，从父组件流向子组件。

每一个组件都维护着同一个状态，那么我们可以将这个状态放在同一个地方

方便我们进行状态的判断



**当你遇到需要同时获取多个子组件数据，或者两个组件之间需要相互通讯的情况时，需要把子组件的 state 数据提升至其共同的父组件当中保存。之后父组件可以通过 props 将状态数据传递到子组件当中。这样应用当中所有组件的状态数据就能够更方便地同步共享了。**



每一个组件的state都是自己私有的，那么点击子组件的时候子组件的state改变了的同时，也想通知父组件，我们常用的做法是，在使用子组件的时候，在上面写上一个回调，传入父组件里面的方法给子组件，在合适的时候调用这个回调。



#### 不可变性是啥？

不可变性使得**复杂的特性更容易实现**。比如回退

如果直接修改数据，那么就很难跟踪到数据的改变。

**跟踪数据的改变**需要可变对象可以与之改变之前的版本进行对比，这样整个对象树都需要被遍历一次。

但是呢，如果用新对象替换旧对象，我们就可以直接判断，对象变了

不可变性**最主要的优势**在于它可以帮助我们在 React 中创建 *pure components*。

我们可以很轻松的确定不可变数据是否发生了改变，从而确定何时对组件进行**重新渲染**。



#### 函数组件

如果你想写的组件只包含一个 `render` 方法，并且不包含 state，那么使用**函数组件**就会更简单。



组件的 key 值并不需要在全局都保证唯一，只需要在当前的同一级元素之前保证唯一即可。

如果是一次性的数据，那就可以直接用索引做key

如果不是，就不要用索引做key，保证是一个唯一的值，如果没有一个唯一的值可以区分，那就要考虑调整数据结构了



## 系统的文档

### JSX简介

将UI和JS混写

遇到大括号就是JS了

里面`class`特性变成了`className`，而且在JSX里面html的特性都是小驼峰的写法了，注意，就算不这么写，也会被转换

给子组件或者是标签传值，双引号里是常量，大括号里面是JS，只有这两种，不能混用

在return后面加上`()`避免被加了`;`

JSX防止注入攻击，ReactDOM在渲染所有输入内容之前，默认都会进行转义

所有的内容在渲染之前都被转换成了字符串。这样可以有效地防止 [XSS（cross-site-scripting, 跨站脚本）](https://en.wikipedia.org/wiki/Cross-site_scripting)攻击。

###元素渲染

React 元素是**不可变对象**

一旦被创建，你就无法更改它的子元素或者属性

一个元素就像电影的单帧：它代表了某个特定时刻的 UI



更新UI的唯一方式是：创建一个新的元素，调用`ReactDOM.render()`

虽然我们的元素是新的，但是react只会去更新需要更新的部分



### 组件

组件，从概念上类似于 JavaScript 函数。它接受任意的入参（即 “props”），并返回用于描述页面展示内容的 React 元素。

#### 函数组件

编写jS函数，返回一个 React 元素，入参是props

#### 类组件

```jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```



#### 自定义组件

当 React 元素为用户自定义组件时，它会将 JSX 所接收的属性（attributes）转换为单个对象传递给组件，这个对象被称之为 “props”。

自定义组件名称必须以大写字母开头。



#### 混合组件

组件可以在其输出中引用其他组件。

这就可以让我们用同一组件来抽象出任意层次的细节。

按钮，表单，对话框，甚至整个屏幕的内容：在 React 应用程序中，这些通常都会以组件的形式表示。



### Props的只读性

决不能修改自身的 props

**所有 React 组件都必须像纯函数一样保护它们的 props 不被更改。**

state 允许 React 组件随用户操作、网络响应或者其他变化而动态更改输出内容。



###State

例子：如何封装真正可复用的 `Clock` 组件。

它将设置自己的计时器并每秒更新一次。

>  state 是私有的，并且完全受控于当前组件

4 Steps 将函数组件改写成类组件

1. 创建一个同名的ES6 Class，并继承于 `React.Component`
2. 添加render方法
3. 将函数体移入 render
4. 在函数体中，用 `this.state` 取代 `props`



每次组件更新时 `render` 方法都会被调用。

但只要在相同的 DOM 节点中渲染 `<Clock />` ，就仅有一个 `Clock` 组件的 class 实例被创建使用。

这就使得我们可以使用如 state 或生命周期方法等很多其他特性。

**render被多次调用，实例化只有一次**



### 生命周期

#### 挂载 mount & 卸载 umount

```jsx
componentDidMount(){}
componentWillUnmount(){}
```



#### state的使用注意事项

1. 不要直接修改state，而是使用`setState()`

2. state的更新可能是异步的，考虑到性能，多个`setState()`的调用进行了合并，所以不要依赖一个的改变而改变另外一个

   要解决这个问题，可以把`setState`里面写成一个函数，箭头函数和普通的函数都OK

   ```jsx
   this.setState((state, props) => ({ // 接收state、props两个参数
     counter: state.counter + props.increment
   }));
   ```

3. state的更新会被合并

   合并是浅合并，state里面没有改变的变量，会被保留

   

### 事件处理

React 事件的命名采用小驼峰式（camelCase）

使用 JSX 语法时你需要传入一个函数作为事件处理函数

```jsx
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

不能通过返回 `false` 的方式阻止默认行为。

必须显式的使用 `preventDefault` 。

```jsx
function ActionLink() {
  function handleClick(e) {
    e.preventDefault(); // 要介样写
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```



下面进入正题：

当使用Class语法的时候，将事件处理函数声明为 class 中的方法

```jsx
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // 为了在回调中使用 `this`，这个绑定是必不可少的
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() { // 声明函数的具体逻辑
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }
  
  handleClick1 = () => { // 直接使用箭头函数，绑定了this，这是实验性语法，慎用
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }

  render() {
    return (
      <!-- 添加事件 -->
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
      <!-- 也可以直接在回调上使用箭头函数,但是作为 prop 传入子组件会带来性能问题，慎用 -->
      <button onClick={(e) => this.handleClick(e)}>
        点击我
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```

事件绑定的函数，都是回调，如果里面用到了this，要手动绑定

你必须谨慎对待 JSX 回调函数中的 `this`，在 JavaScript 中，class 的方法默认不会[绑定](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind)`this`。

如果你忘记绑定 `this.handleClick` 并把它传入了 `onClick`，当你调用这个函数的时候 `this` 的值为 `undefined`。



通常情况下，如果你没有在方法后面添加 `()`，例如 `onClick={this.handleClick}`，你应该为这个方法绑定 `this`。

也可以**直接使用箭头函数(推荐)**

如果是有`()`的，肯定是有返回值的，返回的值可能是方法也可能是一个变量。



此语法问题在于每次渲染 `LoggingButton` 时都会创建不同的回调函数。

如果**该回调函数作为 prop 传入子组件**时，这些组件可能会进行额外的重新渲染。

我们通常建议在构造器中绑定或使用 class fields 语法来避免这类性能问题。



#### 向事件处理函数传递参数

在循环中，通常我们会为事件处理函数传递额外的参数。

例如，若 `id` 是你要删除那一行的 ID，以下两种方式都可以向事件处理函数传递参数：

```jsx
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

在这两种情况下，React 的事件对象 `e` 会被作为第二个参数传递。

如果通过箭头函数的方式，事件对象**必须显式**的进行传递，
而通过 `bind` 的方式，事件对象以及更多的参数将会被**隐式的进行传递**。



### 条件渲染

依据应用的不同状态，你可以只渲染对应状态下的部分内容。

#### 元素变量

你可以使用变量来储存元素。

它可以帮助你有条件地渲染组件的一部分，而其他的渲染部分并不会因此而改变。

```jsx
let button = <LogoutButton onClick={this.handleLogoutClick} />;
return {button}
```



#### JSX中内联条件渲染

##### && 运算符

```jsx
<h1>Hello!</h1>
{unreadMessages.length > 0 &&
  <h2>
  	You have {unreadMessages.length} unread messages.
  </h2>
}
```



##### 三目运算符

```jsx
<div>
  {isLoggedIn ? (
    <LogoutButton onClick={this.handleLogoutClick} />
  ) : (
    <LoginButton onClick={this.handleLoginClick} />
  )}
</div>
```

如果过于复杂，要提取组件



#### 阻止组件渲染

在极少数情况下，你可能希望能隐藏组件，即使它已经被其他组件渲染。

若要完成此操作，你可以让 `render` 方法直接返回 `null`，而不进行任何渲染。



在组件的 `render` 方法中返回 `null` 并不会影响组件的生命周期。

这个示例中，`componentDidUpdate` 依然会被调用。

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

function WarningBanner(props) {
    if(!props.warn){
       return null; // 在组件的 render 方法中返回 null 并不会影响组件的生命周期
    }

    return (
        <div className="warning">
            Warning!
        </div>
    )
}

class Page extends React.Component{
    constructor(props) {
        super(props);
        this.state = {
            showWarning: true
        };
        this.handleToggleClick = this.handleToggleClick.bind(this);
    }

    componentDidUpdate(){
        // 在组件的 render 方法中返回 null 并不会影响组件的生命周期
        // 每次显示/隐藏都会打印
        console.log('componentDidUpdate');
    }

    handleToggleClick(){
        this.setState(state => ({
            // 因为依赖到了state里面的状态来改变state里面的状态，所以用函数代替对象
            showWarning: !state.showWarning
        }));
    }

    render() {
        return (
            <div>
                <WarningBanner warn={this.state.showWarning}/>
                <button onClick={this.handleToggleClick}>
                    {this.state.showWarning? 'Hide' : 'Show'}
                </button>
            </div>
        )
    }
}

ReactDOM.render(
    <Page />,
    document.getElementById('root')
)
```



### 列表

#### 渲染多个组件

可以通过使用 `{}` 在 JSX 内构建一个**元素集合**

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const numbers = [1, 2, 3, 4, 5];
// const list = numbers.map((number) =>
//     <li>{number}</li>
// );

function NumberList (props) {
    const numbers = props.numbers;
    const listItems = numbers.map((number) =>
        // key 帮助 React 识别哪些元素改变了
        // 一个元素的 key 最好是这个元素在列表中拥有的一个独一无二的字符串
        // 通常，我们使用来自数据 id 来作为元素的 key
        <li key={number.toString()}>{number}</li>
    );
    return (
        <ul>{listItems}</ul>
    )
}

ReactDOM.render(
    <NumberList numbers={numbers}/>,
    document.getElementById('root')
)
```

使用索引作为key，假如这个列表项目的顺序可能会变化，就不要使用索引做key了，可能会引发性能问题。

```jsx
const todoItems = todos.map((todo, index) =>
  // Only do this if items have no stable IDs
  <li key={index}>
    {todo.text}
  </li>
);
```

有两篇文章可以看一下

[深度解析使用索引作为 key 的负面影响](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318) 

[深入解析为什么 key 是必须的](https://zh-hans.reactjs.org/docs/reconciliation.html#recursing-on-children)



#### 提取组件，元素的 key 只有放在就近的数组上下文中才有意义

如果你提取出一个 `ListItem` 组件，你应该把 key 保留在数组中的这个 `<ListItem />` 元素上，而不是放在 `ListItem` 组件中的 `<li>` 元素上。

```jsx
function ListItems(props) {
    return <li>{props.value}</li>
}

function NumberList(props) {
    const numbers = props.numbers;
    const listItems = numbers.map((number) =>
        // key 写在这里
        <ListItems key={number.toString()}
                   value={number} />
    )
    return (
        <ul>
            {listItems}
        </ul>
    )
}
```



#### key只是在兄弟节点之间必须唯一

也就是不同的数组间，某2项的key值重复，没有关系

保证同一个列表、数组的项之间唯一就可以了



key只会传递信息给react，不会传给我，所以如果我需要这个key绑定的值，再声明一个特性来接收。



#### 将map写入jsx

```jsx
return (
        <ul>
            {numbers.map((number) =>
                <ListItems key={number.toString()}
                           value={number} />
            )}
        </ul>
    )
```



### 表单

在 React 里，HTML 表单元素的工作方式和其他的 DOM 元素有些不同，

这是因为表单元素通常会保持一些内部的 state。

#### 受控组件

使 React 的 state 成为“唯一数据源”。

渲染表单的 React 组件还控制着用户输入过程中表单发生的操作。

被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”。



在表单上设置value属性，因此显示的值将始终为 `this.state.value`，

这使得 React 的 state 成为唯一数据源。

由于 `handlechange` 在每次按键时都会执行并更新 React 的 state，

因此显示的值将随着用户输入而更新。



这使得修改和验证用户的输入都变的简单。



#### textarea标签

`<textarea>` 使用 `value` 属性代替里面的子元素文本

别的就和input的用法一样了



#### select标签



#### 文件input标签



#### 处理多个输入



#### 受控输入空值



#### 受控组件的替代



#### 成熟的解决方案

https://jaredpalmer.com/formik/

## 状态提升





## 组合&继承





## 规范和思想





## 进阶





## Hook