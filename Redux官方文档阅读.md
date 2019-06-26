## 起步

### 三大原则

#### 单一数据源

**整个应用的 state 被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store 中。**



#### State是只读的

**唯一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象。**

```js
store.dispatch({
  type: 'COMPLETE_TODO',
  index: 1
})
```



#### 使用纯函数来执行修改

**为了描述 action 如何改变 state tree ，你需要编写 reducers。**

Reducer 只是一些纯函数，它接收先前的 state 和 action，并返回新的 state。

刚开始你可以只有一个 reducer，随着应用变大，你可以把它拆成多个小的 reducers，分别独立地操作 state tree 的不同部分。

因为 reducer 只是函数，你可以控制它们被调用的顺序，传入附加数据，甚至编写可复用的 reducer 来处理一些通用任务，如分页器。



```js
import { combineReducers, createStore } from 'redux'

function visibilityFilter(state = 'SHOW_ALL', action) {
  switch (action.type) {
    case 'SET_VISIBILITY_FILTER':
      return action.filter
    default:
      return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case 'COMPLETE_TODO':
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: true
          })
        }
        return todo
      })
    default:
      return state
  }
}

let reducer = combineReducers({ visibilityFilter, todos })
let store = createStore(reducer)
```



##基础

### Action

**Action** 是把数据从应用传到 store 的有效载荷。

它是 store 数据的**唯一**来源。

一般来说你会通过 [`store.dispatch()`](http://cn.redux.js.org/docs/api/Store.html#dispatch) 将 action 传到 store。

除了 `type` 字段外，action 对象的结构完全由你自己决定。

action 内必须使用一个字符串类型的 `type` 字段来表示将要执行的动作。



这时，我们还需要再添加一个 action index 来表示用户完成任务的动作序列号。因为数据是存放在数组中的，所以我们通过下标 `index` 来引用特定的任务。

而实际项目中一般会在新建数据的时候生成唯一的 ID 作为数据的引用标识。

**我们应该尽量减少在 action 中传递的数据**。

比如上面的例子，传递 `index` 就比把整个任务对象传过去要好。



### Reducer

Reducers 指定了应用状态的变化如何响应 actions 并发送到 store 的，**记住 actions 只是描述了有事情发生了这一事实，并没有描述应用如何更新 state**。



##### 处理 Reducer 关系时的注意事项

开发复杂的应用时，不可避免会有一些数据相互引用。建议你尽可能地把 state 范式化，不存在嵌套。

把所有数据放到一个对象里，每个数据以 ID 为主键，不同实体或列表间通过 ID 相互引用数据。

把应用的 state 想像成数据库。





## 高级

