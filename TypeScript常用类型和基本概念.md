## 原始数据类型

布尔值、数值、字符串、`null`、`undefined` 以及 [ `Symbol`](http://es6.ruanyifeng.com/#docs/symbol)。



###布尔值 

```ts
let isDone: boolean = false;

// boolean 是基本数据类型
// Boolean 是构造函数，实例化的是个 Boolean对象
```



###数值 

```ts
let decLiteral: number = 6;
let decLiteral: number = NaN;
let decLiteral: number = Infinity;
```



###字符串 

```ts
let myName: string = 'Tom';
```



###`Viod`&`null`&`undefined`

给一个变量定义空`viod`没啥意义，因为空，可以定义`null`或者`undefined`

`viod`可以定义`function`没有返回值



`null`类型只能被赋值null

`undefined`类型只能被赋值undefined



他们俩和viod的区别是：

**`undefined` 和 `null` 是所有类型的子类型**

这俩可以赋值给所有别的定义类型的值。

但是**viod并不能**



## 任意值

**任意值**（Any）用来表示允许赋值为任意类型。

在**任意值**上访问任何属性都是允许的，也允许调用任何方法。

**声明一个变量为任意值之后，对它的任何操作，返回的内容的类型都是任意值**。



一个变量在声明的时候未指定类型，那么它会被识别为任意值类型



## 类型推论 `Type Inference`

TypeScript 会在没有明确的指定类型的时候推测出一个类型，这就是类型推论。

**如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成** `any` **类型而完全不被类型检查**



## 联合类型 `Union Types`

联合类型使用 `|` 分隔每个类型。

当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们**只能访问此联合类型的所有类型里共有的属性或方法**

访问**不是共有**的属性会报错。

联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型

之后就**只能访问被赋值的类型**的属性和方法



## 对象的类型 - 接口 `Interfaces`

使用接口（`Interfaces`）来定义对象的类型。

`Interfaces`是对行为的抽象，而具体如何行动需要由类（`classes`）去实现（`implement`）。

TypeScript 中的接口是一个非常灵活的概念，除了可用于[对类的一部分行为进行抽象]()以外，也常用于对「对象的形状（Shape）」进行描述。

```ts
interface Person {
    name: string;
    age: number;
}


let tom: Person = {
    name: 'Tom',
    age: 25
};
```

我们约束了 `tom` 的形状必须和接口 `Person` 一致。

接口一般首字母大写。[有的编程语言中会建议接口的名称加上 `I` 前缀](https://msdn.microsoft.com/en-us/library/8bc1fexb(v=vs.71).aspx)。



赋值的时候，变量的形状必须和接口的形状保持一致，少和多一些属性都是不行的。



####可选属性

可选属性，可以不存在，但仍然不可以添加未定义的属性

```ts
interface Person {
    name: string;
    age?: number;
}

let tom: Person = {
    name: 'Tom'
};
```



#### 任意属性

一个接口允许有任意的属性

```ts
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    name: 'Tom',
    gender: 'male'
};
```

使用 `[propName: string]` 定义了任意属性取 `string` 类型的值。



需要注意的是，**一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集**



#### 只读属性

有时候我们希望对象中的一些字段**只能在创建的时候被赋值**，那么可以用 `readonly` 定义只读属性

只读的约束存在于第一次给对象赋值的时候

假如第一次给对象赋值的时候，只读属性没有赋值，那么以后也不能赋值了。

而且不赋值是不可以的。



## 数组的类型

数组类型有多种定义方式，比较灵活。




## 函数的类型


## 类型断言


## 声明文件


## 内置对象

