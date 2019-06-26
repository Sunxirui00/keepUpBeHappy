## 混入

### 基础

来分发 Vue 组件中的可复用功能。

一个混入对象可以包含任意组件选项。

当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。

```js
let myMixin = {
  created () {
    this.hello()
  },
  methods: {
    hello () {
      console.log('hello from mixin!')
    }
  }
}
export default {
  name: 'HelloWorld',
  mixins: [myMixin]
}
```



### 选项合并
数据对象在内部会进行递归合并，并在发生冲突时以组件数据优先。
```js
let myMixin = {
  data: function () {
    return {
      message: 'hello',
      foo: 'abc'
    }
  }
}
```



同名钩子函数将合并为一个数组，因此都将被调用。
另外，混入对象的钩子将在组件自身钩子之前调用。

```js
let myMixin = {
  created () {
    console.log('混入对象的钩子被调用')
  }
}
```



值为对象的选项，例如 methods、components 和 directives，将被合并为同一个对象。
两个对象键名冲突时，取组件对象的键值对。

```js
let myMixin = {
  methods: {
    foo () {
      console.log('foo')
    },
    conflicting () {
      console.log('from mixin')
    }
  }
}
```



### 全局混入

混入也可以进行全局注册。
使用恰当时，这可以用来为自定义选项注入处理逻辑。
因为它会影响每个单独创建的 Vue 实例 (包括第三方组件)。
大多数情况下，只应当应用于自定义选项，就像上面示例一样。
推荐将其作为插件发布，以避免重复应用混入。
```js
Vue.mixin({
  created () {
    let myOption = this.$options.myOption;
    if(myOption){
      console.log(myOption)
    }
  }
})
```

### 自定义选项合并策略

还可以自己写选项合并策略
如果想让自定义选项以自定义逻辑合并，可以向 Vue.config.optionMergeStrategies 添加一个函数




## 渲染函数 & jsx

###基础

```vue
<template>
  <anchored-heading :level="2">Hello render!</anchored-heading>
</template>

<script>
import Vue from 'vue'

Vue.component('anchored-heading', {
  render: function (createElement) {
    //createElement 的返回值是一个虚拟 DOM
    return createElement(
      'h' + this.level,   // 标签名称
      this.$slots.default // 子节点数组，这里要熟悉实例属性API
    )
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})

export default {
    name: 'Render'
}
</script>
```

###虚拟DOM

每个元素都是一个节点。

每段文字也是一个节点。

甚至注释也都是节点。

一个节点就是页面的一个部分。

就像家谱树一样，每个节点都可以有孩子节点 (也就是说每个部分可以包含其它的一些部分)。



`createElement`返回的就是一个虚拟DOM



### createElement 参数

参数一(必填)：

一个 HTML **标签名**（eg: div）、组件选项对象，或者 `resolve` 了上述任何一种的一个 `async` 函数



参数二(选填)：

一个与模板中属性对应的**数据对象**。

包含：

1. 类名'class'，不要忘记冒号！
2. 样式style
3. 特性attrs
4. 组件props
5. 属性domProps（eg: innerHTML）
6. 事件监听on（eg: click），不再支持`v-on:keyup.enter`这样的修饰器，需要手动实现
7. 原生事件监听nativeOn，仅用于组件，不能用于组件内部，`vm.$emit`触发
8. 自定义指令directives（[{name:'xxx', etc指令属性}]），你无法对 `binding` 中的 `oldValue` 赋值，因为 Vue 已经自动为你进行了同步
9. 作用域插槽scopedSlots
10. 插槽名称slot
11. 其他特殊顶层属性（eg:key\ref）
12. 渲染函数中给多个元素都应用了相同的 ref 名，那么 `$refs.myRef` 会变成一个数组 refInFor: true



参数三(选填)：

子级虚拟节点 (VNodes)，由 `createElement()` 构建而成

也可以使用字符串来生成“文本虚拟节点”



#### 数据对象

举个例子

实现一个这样的`<a>`标签

```html
<h1>
  <a name="hello-world" href="#hello-world">
    Hello world!
  </a>
</h1>
```

这些写render函数

```js
Vue.component('anchored-heading', {
  render: function (createElement) {
    // 创建 kebab-case 风格的 ID
    var headingId = getChildrenTextContent(this.$slots.default)
      .toLowerCase()
      .replace(/\W+/g, '-')
      .replace(/(^-|-$)/g, '')

    return createElement( // 创建一个虚拟DOM
      'h' + this.level, // 第一个参数：this.level，是props
      [ // 第三个参数：子级虚拟节点
        createElement(
          'a', // 第一个参数：创建一个虚拟DOM，a
          { //  第二个参数：数据对象
            attrs: { // VNode的特性，2个
              name: headingId,
              href: '#' + headingId
            }
        	}, 
          this.$slots.default) // 第三个参数：子级虚拟节点，slot里面的内容
      ]
    )
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
}) 
```



#### 注意：VNode 必须唯一

如果想重复使用，写成map

```js
Array.apply(null, { length: 20 }).map(function () {
  return createElement('p', 'hi')
})
```

下面是错的

```js
return createElement('div', [
  // 错误 - 重复的 VNode
	myParagraphVNode, myParagraphVNode
])
```



### 使用 JavaScript 代替模板功能

#### v-if/v-for

渲染函数的实现

用 JavaScript 的 `if`/`else` 和 `map` 来重写：

```js
props: ['items'],
render: function (createElement) {
  if (this.items.length) {
    return createElement('ul', this.items.map(function (item) {
      return createElement('li', item.name)
    }))
  } else {
    return createElement('p', 'No items found.')
  }
}
```

写成jsx

```jsx
props: ['items'],
render: function (h) {
  if (this.items.length) {
     return ( 
       <ul>
         <li>{item.name}</li>
       </ul>
     )
  } else {
    return (
      <p>No items found.</p>
    )
  }
}
```



#### v-model

渲染函数的实现

```js
props: ['value'],
render: function (createElement) {
  var self = this
  return createElement('input', {
    domProps: {
      value: self.value
    },
    on: {
      input: function (event) {
        self.$emit('input', event.target.value)
      }
    }
  })
}
```

jsx的实现

```jsx
props: ['value'],
render: function (h) {
  return (
    <input 
      value={this.value} 
      onInput={(e) => {this.$emit('input', e.target.value)}}
    ></input>
  )
}
```



#### 事件&按键修饰符

对于 `.passive`、`.capture` 和 `.once` 这些事件修饰符, Vue 提供了相应的前缀可以用于 `on`：

| 修饰符                             | 前缀 |
| :--------------------------------- | :--- |
| `.passive`                         | `&`  |
| `.capture`                         | `!`  |
| `.once`                            | `~`  |
| `.capture.once` 或 `.once.capture` | `~!` |

例如:

```js
on: {
  '!click': this.doThisInCapturingMode,
  '~keyup': this.doThisOnce,
  '~!mouseover': this.doThisOnceInCapturingMode
}
```

别的修饰符，需要写成事件方法



#### 插槽

通过 [`this.$slots`](https://cn.vuejs.org/v2/api/#vm-slots) 访问静态插槽的内容，每个插槽都是一个 VNode 数组

通过 [`this.$scopedSlots`](https://cn.vuejs.org/v2/api/#vm-scopedSlots) 访问作用域插槽，每个作用域插槽都是一个返回若干 VNode 的函数



如果要用渲染函数向子组件中传递作用域插槽，可以利用 VNode 数据对象中的 `scopedSlots`字段



### JSX

将 `h` 作为 `createElement` 的别名是 Vue 生态系统中的一个通用惯例，实际上也是 JSX 所要求的。

从 Vue 的 Babel 插件的 3.4.0 版本开始。

含有 JSX 的任何方法和 getter 中 (不是函数或箭头函数中) 自动注入 `const h = this.$createElement`

这样你就可以去掉 `(h)` 参数了。



###函数式组件

没有响应式数据，也没有实例，就可以写成函数式组件。

> 我们可以将组件标记为 `functional`，这意味它无状态 (没有 **响应式数据** )，也没有实例 (没有 `this` 上下文)。