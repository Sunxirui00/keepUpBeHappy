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

### 定义方式

数组类型有多种定义方式，比较灵活。

#### 「类型 + 方括号」来表示数组：

```ts
let fibonacci: number[] = [1, 1, 2, 3, 5];
```

联合类型与数组结合：

```ts
let fibonacci: (number | string)[] = [1, 1, 2, 3, 5];
```

声明的时候不可以跨越类型，后续对数组的操作也不可以，同样会报错。

```ts
let fibonacci: number[] = [1, 1, 2, 3, 5];
fibonacci.push('8');
```

报错

#### 数组泛型 Array Generic

`Array<elemType>` 来表示数组：

```ts
let fibonacci: Array<number> = [1, 1, 2, 3, 5];
```

#### 接口表示数组

```ts
interface NumberArray {
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```

`NumberArray` 表示：只要 `index` 的类型是 `number`，那么值的类型必须是 `number`。



#### `any`在数组中的应用

`any` 表示数组中允许出现任意类型：

```ts
let list: any[] = ['Xcat Liu', 25, { website: 'http://xcatliu.com' }];
```



#### 类数组 Array-like Object

类数组不是数组类型，比如 `arguments`

常见的类数组都有自己的接口定义，如 `IArguments`, `NodeList`, `HTMLCollection` 

## 函数的类型

### 声明方式

一个函数有输入和输出，要在 TypeScript 中对其进行约束，需要把输入和输出都考虑到

#### 函数声明 Function Declaration

```ts
function sum(x: number, y: number): number {
    return x + y;
}
```

输入多余的（或者少于要求的）参数，不可以

#### 函数表达式 Function Expression

按照js的写法，声明一个匿名函数，然后赋值给一个变量，也是可以的。

实际应该是这么写：

```ts
let mySum: (x: number, y: number) => number = function (x: number, y: number): number {
    return x + y;
};
```

这里的箭头不是ES6的箭头，ts里是函数定义的意思。

左边是输入类型，需要用括号括起来，右边是输出类型。

#### 用接口定义函数的形状

```ts
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
```



### 可选参数`?`、参数默认值`=`、剩余参数`...`

```ts
function buildName(firstName: string, lastName?: string) {
    if (lastName) {
        return firstName + ' ' + lastName;
    } else {
        return firstName;
    }
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');
```

如果可选参数**没有设置默认值**，就必须写在必须参数的后面。

如果设置了默认值，就没有限制了。

```ts
function buildName(firstName: string = 'Tom', lastName: string) {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let cat = buildName(undefined, 'Cat');		
```

剩余参数，rest参数参考ES6

这里可以给剩余参数加一个数组的类型的定义，剩余参数是一个数组。

```ts
function push(array: any[], ...items: any[]) {
    items.forEach(function(item) {
        array.push(item);
    });
}

let a = [];
push(a, 1, 2, 3);
```



### 重载

**重载允许一个函数接受不同数量或类型的参数时，作出不同的处理。**

多态？？

重复定义了多次函数 ，

前几次都是函数定义，

最后一次是函数实现。

TypeScript 会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面。

```ts
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```



## 类型断言 Type Assertion

可以用来手动指定一个值的类型

语法：

```ts
<类型>值  ||  值 as 类型
```

在tsx（react的jsx语法的ts版）中必须用`as`

类型断言不是类型转换，**断言成一个联合类型中不存在的类型是不允许的**

用在，联合类型先指定一个类型

这样就可以在我们不确定类型的时候，访问其中一种类型的属性或方法了。

```ts
function getLength(something: string | number): number {
    if ((<string>something).length) {
        return (<string>something).length;
    } else {
        return something.toString().length;
    }
}
```



## 声明文件

当使用第三方库时，我们需要引用它的声明文件，才能获得对应的代码补全、接口提示等功能。

#### 什么是声明语句

```ts
declare var jQuery: (selector: string) => any;

jQuery('#foo');
```

`declare var` 并没有真的定义一个变量，只是定义了全局变量 `jQuery` 的类型，仅仅会用于编译时的检查，在编译结果中会被删除。

#### 什么是声明文件

通常我们会把**声明语句**放到一个**单独的文件**（`jQuery.d.ts`）中，这就是声明文件

声明文件必需以 `.d.ts` 为后缀。

[全局变量的模式引入声明文件](https://ts.xcatliu.com/basics/declaration-files)

还有模块导入的方式。

推荐使用 `@types` 统一管理第三方库的声明文件。

直接用 npm 安装对应的声明模块即可

```ts
npm install @types/jquery --save-dev
```

[声明文件检索TypeSearch]([http://microsoft.github.io/TypeSearch/](http://microsoft.github.io/TypeSearch/))

#### 书写声明文件

当一个第三方库没有提供声明文件时，我们就需要自己书写声明文件了。

[书写声明文件](https://ts.xcatliu.com/basics/declaration-files#zi-dong-sheng-cheng-sheng-ming-wen-jian)

#### 自动生成声明文件

如果库的源码本身就是由 ts 写的，那么在使用 `tsc` 脚本将 ts 编译为 js 的时候，添加 `declaration` 选项，就可以同时也生成 `.d.ts` 声明文件了。

我们可以在命令行中添加 `--declaration`（简写 `-d`），或者在 `tsconfig.json` 中添加 `declaration` 选项。



## 内置对象

内置对象是指根据标准在全局作用域（Global）上存在的对象。

标准有ECMAScript 和浏览器环境的DOM&BOM标准。

ECMAScript 标准提供的内置对象有：

`Boolean`、`Error`、`Date`、`RegExp` 等。

DOM 和 BOM 提供的内置对象有：

`Document`、`HTMLElement`、`Event`、`NodeList` 等。

Node.js 不是内置对象的一部分，如果想用 TypeScript 写 Node.js，则需要引入第三方声明文件。

