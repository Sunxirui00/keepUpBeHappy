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

带 `selected` 属性，选项默认被选中

React 并不会使用 `selected` 属性，而是在根 `select` 标签上使用 `value` 属性

这在受控组件中更便捷，因为您只需要在根标签中更新它



你可以将数组传递到 `value` 属性中，以支持在 `select` 标签中选择多个选项：

```jsx
<select multiple={true} value={['B', 'C']}>
```



#### 文件input标签

`<input type=“file”>` 允许用户从存储设备中选择一个或多个文件，将其上传到服务器，或通过使用 JavaScript 的 File API 进行控制。

因为它的 value 只读，所以它是 React 中的一个**非受控**组件。


#### 处理多个输入

当需要处理多个 `input` 元素时，可以给每个元素添加 `name` 属性

并让处理函数根据 `event.target.name` 的值选择要执行的操作

```jsx
handleInputChange (e) {
  const name = e.target.name;
  const value = e.target.type === 'checkbox' ? e.target.checked : e.target.value;

  this.setState({
    [name]: value
  })
}
```



#### 受控输入空值

在[受控组件](https://zh-hans.reactjs.org/docs/forms.html#controlled-components)上指定 value 的 prop 可以防止用户更改输入。

如果指定了 `value`，但输入仍可编辑，则可能是意外地将`value` 设置为 `undefined` 或 `null`。

```jsx
// 指定了input的value为固定的字符串，可以防止用户更改内容
ReactDOM.render(<input type="text" value="hi"/>, document.getElementById('root'));

setTimeout(function () {
  	// 这样就可以输入了
    ReactDOM.render(<input type="text" value={null}/>, document.getElementById('root'))
```



#### 受控组件的替代 - 非受控组件

可以 [使用 ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 来从 DOM 节点中获取表单数据。



#### 成熟的解决方案

https://jaredpalmer.com/formik/



### 状态提升

通常，多个组件需要反映相同的变化数据，

这时我们建议将共享状态提升到最近的共同父组件中去。



这里兄弟组件共享父组件里面的数据，也可以改变父组件的值，是**通过调用父组件的方法**。

不一样的组件，传入的入参的回调不同，来区别调用的个体。



父组件的方法也是通过props传入子组件的，子组件接收到，声明一个自己的方法来接收，或者在合适的时候调用。



### 组合&继承 - 代码的重用

React 有十分强大的组合模式。

我们推荐使用组合而非继承来实现组件间的代码重用。



类似于vue的插槽，这些组件，使用`props.children`来将他们的子组件传递到渲染结果中。

```jsx
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      <!-- 会被渲染到这里 -->
      {props.children}
    </div>
  );
}
```



```jsx
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <!-- 这里的内容 - start -->
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
      <!-- 这里的内容 - end -->
    </FancyBorder>
  );
}
```



`<FancyBorder>` JSX 标签中的所有内容都会作为一个 `children` prop 传递给 `FancyBorder`组件。

因为 `FancyBorder` 将 `{props.children}` 渲染在一个 `<div>` 中，

被传递的这些子组件最终都会出现在输出结果中。



少数情况下，你可能需要在一个组件中预留出**几个“洞”**。

这种情况下，我们可以不使用 `children`，

而是自行约定：将所需内容传入 props，并使用相应的 prop。



```jsx
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
    <!-- 使用标签特性预留了2个“洞” -->
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

`<Contacts />` 和 `<Chat />` 之类的 React 元素本质就是对象（object），

所以你可以把它们当作 props，像其他数据一样传递。



这种方法可能使你想起别的库中“槽”（slot）的概念，

但在 React 中没有“槽”这一概念的限制，你可以将任何东西作为 props 进行传递。



#### 组件的特殊实例

“特殊”组件可以通过 props 定制并渲染“一般”组件：

```jsx
function Dialog(props) { // 一般组件
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

function WelcomeDialog() { // 特殊组件，又包了一层
  return (
    <Dialog
      title="Welcome"
      message="Thank you for visiting our spacecraft!" />
  );
}
```



#### 继承

如果你想要在组件间复用非 UI 的功能，我们建议将其提取为一个单独的 JavaScript 模块，如函数、对象或者类。

组件可以直接引入（import）而无需通过 extend 继承它们。



如果是复用UI功能，Props和组合就够了。



### 规范和思想

一个组件原则上只能负责一个功能。

如果它需要负责更多的功能，这时候就应该考虑将它拆分成更小的组件。



在拿到设计图的时候，就要给组件划分层级了。



大页面，从下往上写；小页面，从上往下写。



静态页面和动态交互分开写。



#### state和props的区别

**state**：使你的 UI 具备交互功能，需要有触发基础数据模型改变的能力

**props**:  是父组件向子组件传递数据的方式



通过问自己以下三个问题，你可以逐个检查相应数据是否属于 state：

1. 该数据是否是由父组件通过 props 传递而来的？如果是，那它应该不是 state。
2. 该数据是否随时间的推移而保持不变？如果是，那它应该也不是 state。
3. 你能否根据其他 state 或 props 计算出该数据的值？如果是，那它也不是 state。

#### 确定 state 放置的位置

对于应用中的每一个 state：

- 找到根据这个 state 进行渲染的**所有组件**。
- 找到他们的**共同所有者**（common owner）组件（在组件层级上高于所有需要该 state 的组件）。
- 该共同所有者组件或者比它层级更高的组件应该拥有该 state。
- **如果你找不到一个合适的位置来存放该 state，就可以直接创建一个新的组件来存放该 state，并将这一新组件置于高于共同所有者组件层级的位置。**



就官方的例子来分析，有2个state，一个是**过滤词**`filterText`一个是**是否有库存**`inStockOnly`

然后要思考2件事儿，一是放在哪个组件里，二是交互，谁来改变他们

一 - 位置：

搜索框要用，列表过滤也要用，就放在他们的父组件里。

二 - 交互：

用户是在搜索框里输入过滤词`filterText`，点选单选按钮来选择是否有库存`inStockOnly`，交互都发生在搜索组件。

使用props传递一个回调给搜索框，触发父组件里面的方法来改变state。



## 进阶





## Hook

###概述

**什么时候我会用 Hook？** 如果你在编写函数组件并意识到需要向其添加一些 state，以前的做法是必须将其它转化为 class。

现在你可以在现有的函数组件中使用 Hook。



###[State Hook](https://zh-hans.reactjs.org/docs/hooks-state.html)

以前如果想声明一个state就要把函数换成类

现在我们可以在函数里声明state了

三个要点：

声明：

````jsx
import React, { useState } from 'react';

function Example () {
	// 声明一个叫 “count” 的 state 变量
	const [count, setCount] = useState(0)
}
````

访问：

```jsx
<p>{count}</p>
```

更新：

```jsx
<button onClick={() => setCount(count + 1)}>Click</button>
```



###[Effect Hook](https://zh-hans.reactjs.org/docs/hooks-effect.html)

数据获取（初始化的一些数据：Ajax，new Class之类的，就是在页面初始化、销毁、更新的时候会做的一些事情），设置订阅（事件）以及手动更改 React 组件（操作DOM）中的 DOM 都属于副作用。



#### 需要清除的effect和不需要清除的effect

有时候，我们只想**在 React 更新 DOM 之后运行一些额外的代码。**

比如发送网络请求，手动变更 DOM，记录日志，这些都是常见的无需清除的操作。

因为我们在执行完这些操作之后，就可以忽略他们了。



**useEffect 做了什么？** 

通过使用这个 Hook，你可以告诉 React 组件需要在**渲染后执行某些操作**。

**React 会保存你传递的函数（我们将它称之为 “effect”），并且在执行 DOM 更新之后调用它。**

在这个 effect 中，我们设置了 document 的 title 属性，不过我们也可以执行数据获取或调用其他命令式的 API。



**为什么在组件内部调用 useEffect？**

 **将 `useEffect` 放在组件内部让我们可以在 effect 中直接访问 `count` state 变量（或其他 props）。**

我们不需要特殊的 API 来读取它 —— 它已经保存在函数作用域中。

Hook 使用了 JavaScript 的闭包机制，而不用在 JavaScript 已经提供了解决方案的情况下，还引入特定的 React API。

**useEffect 会在每次渲染后都执行吗？

 是的，默认情况下，它在第一次渲染之后*和*每次更新**之后**都会执行。



与 `componentDidMount` 或 `componentDidUpdate` 不同：

使用 `useEffect` 调度的 effect 不会阻塞浏览器更新屏幕，这让你的应用看起来响应更快。



### Hook规则

只在最顶层使用Hook，只在react函数里面调用Hook或者在自定义Hook里调用

可以安装ESLint插件



### 自定义Hook

通过自定义 Hook，可以将组件逻辑提取到可重用的函数中。

#### 提取

**自定义 Hook 是一个函数，其名称以 “use” 开头，函数内部可以调用其他的 Hook。** 

将变量提取为入参，将我们想获取的值，用return返回。

自定义 Hook 不需要具有特殊的标识，但是它的名字应该始终以 `use` 开头

#### 使用

**在两个组件中使用相同的 Hook 会共享 state 吗？**不会。

```jsx
function ChatRecipientPicker() {
  const [recipientID, setRecipientID] = useState(1);
  // recipientID 值被传入 useFriendStatus，每次recipientID不同，获取相应的返回值
  const isRecipientOnline = useFriendStatus(recipientID);

  return (
    <>
      <Circle color={isRecipientOnline ? 'green' : 'red'} />
      <select
        value={recipientID}
        onChange={e => setRecipientID(Number(e.target.value))}
      >
        {friendList.map(friend => (
          <option key={friend.id} value={friend.id}>
            {friend.name}
          </option>
        ))}
      </select>
    </>
  );
}
```



### Hook API

####更新state - useState

1. 如果新的state值依赖旧的值，就写成回调，回调的第一个参数就是旧值
2. 不同于`class`类型的`setState({})`方法，是不会进行对象的合并的。所以想同时更新多个值，1. 可以使用`…`扩展运算符来达到**合并更新对象**的效果；2. `useReducer` 是另一种可选方案，它更适合用于管理包含多个子值的 state 对象。
3. `initialState` 参数只会在组件的初始渲染中起作用，后续渲染时会被忽略。如果初始 state 需要通过复杂计算获得，则可以传入一个函数，在函数中计算并返回初始的 state，此函数只在初始渲染时被调用
4. 调用 State Hook 的更新函数并传入当前的 state 时，React 将跳过子组件的渲染及 effect 的执行。



####作用操作 - useEffect

在函数组件主体内（这里指在 React 渲染阶段）改变 DOM、添加订阅、设置定时器、记录日志以及执行其他包含副作用的操作都是不被允许的，因为这可能会产生莫名其妙的 bug 并破坏 UI 的一致性。

使用 `useEffect` 完成副作用操作。



虽然 `useEffect` 会在浏览器绘制后延迟执行，但会保证在任何新的渲染前执行。React 将在组件更新前刷新上一轮渲染的 effect。

##### 执行条件

仅需要在**某些props 改变时**重新创建。

要实现这一点，可以给 `useEffect` 传递第二个参数，它是 effect 所依赖的值数组。

更新后的示例如下：

```jsx
useEffect(
  () => {
    const subscription = props.source.subscribe();
    return () => {
      subscription.unsubscribe();
    };
  },
  [props.source], // 这里写，只有当 props.source 改变后才会重新创建订阅。
);
```



如果想执行只运行一次的 effect（仅在组件挂载和卸载时执行），可以传递一个空数组（`[]`）作为第二个参数。这就告诉 React 你的 effect 不依赖于 props 或 state 中的任何值，所以它永远都不需要重复执行。这并不属于特殊情况 —— 它依然遵循输入数组的工作方式。



如果你传入了一个空数组（`[]`），effect 内部的 props 和 state 就会一直拥有其初始值。尽管传入 `[]` 作为第二个参数有点类似于 `componentDidMount` 和 `componentWillUnmount` 的思维模式，但我们有 [更好的](https://zh-hans.reactjs.org/docs/hooks-faq.html#is-it-safe-to-omit-functions-from-the-list-of-dependencies) [方式](https://zh-hans.reactjs.org/docs/hooks-faq.html#what-can-i-do-if-my-effect-dependencies-change-too-often) 来避免过于频繁的重复调用 effect。除此之外，请记得 React 会等待浏览器完成画面渲染之后才会延迟调用 `useEffect`，因此会使得额外操作很方便。



 [`useLayoutEffect`](https://zh-hans.reactjs.org/docs/hooks-reference.html#uselayouteffect) Hook 来处理不会被延迟的 effect。































