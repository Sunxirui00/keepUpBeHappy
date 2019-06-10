### 类型别名

类型别名用来给一个类型起个新名字。

使用`type`创建类型别名，常用于联合类型。

```ts
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName (n: NameOrResolver): Name {
  if (typeof n === 'string') {
        return n;
    } else {
        return n();
    }
}
```



### 字符串字面量类型

约束取值

字符串字面量类型用来**约束取值**只能是某几个字符串中的一个。

```ts
type EventNames = 'click' | 'scroll' | 'mousemove';
function handleEvent(ele: Element, event: EventNames) {
    // do something
}
```

**类型别名与字符串字面量类型都是使用** `type` **进行定义。**



------



### 元组 Tuple

数组合并了相同类型的对象，而元组合并了**不同类型的对象**。

元组起源于函数编程语言,在这些语言中频繁使用元组。

定义一对值分别为 `string` 和 `number` 的元组：

```ts
let xcatliu: [string, number] = ['Xcat Liu', 25];
```

当直接对元组类型的变量进行初始化或者赋值的时候，

需要提供所有元组类型中指定的项。

也就说，声明了2种类型，就至少要有2个值，分别是这两种类型的。

```ts
let xcatliu: [string, number];
xcatliu = ['Xcat Liu', 25];
```

#### 越界的元素

当添加**越界的元素**时，它的类型会被限制为元组中每个类型的**联合类型**

就是给数组添加新元素的时候，数组变长了，那这个新元素的类型，被限制了。



------



### 枚举 Enum

**枚举类型**用于取值被限定在一定范围内的场景，比如一周只能有七天，颜色限定为红绿蓝等。

枚举使用 `enum` 关键字来定义

```ts
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};
console.log(Days["Sun"] === 0); // true
console.log(Days[0] === "Sun"); // true
```

枚举成员会被**赋值**为从 `0` 开始递增的数字，

同时也会对枚举值到枚举名进行**反向映射**

js的话，是这样的：

```js
Days[Days["Sun"] = 0] = "Sun";
```



#### 手动赋值

```ts
enum Days {Sun = 7, Mon = 1, Tue, Wed, Thu, Fri, Sat};
```

未手动赋值的枚举项会接着上一个枚举项递增。

```ts
console.log(Days["Tue"] === 2); // true
```

未手动赋值的枚举项与手动赋值的重复了，TypeScript 是不会察觉到这一点

直接被后来的值覆盖掉

手动赋值的枚举项可以不是数字，此时需要使用类型断言来让 tsc 无视类型检查 (编译出的 js 仍然是可用的)

```ts
enum Days {Sun = 7, Mon, Tue, Wed, Thu, Fri, Sat = <any>"S"};
```

当然，手动赋值的枚举项也可以为小数或负数，此时后续未手动赋值的项的递增步长仍为 `1`

```ts
enum Days {Sun = 7, Mon = 1.5, Tue, Wed, Thu, Fri, Sat};

console.log(Days["Tue"] === 2.5); // true
```



#### 常数项 & 计算所得项

枚举项的分类

之前的都是常数项，计算所得项以下：

```ts
enum Color {Red, Green, Blue = "blue".length};
```

`"blue".length` 就是一个计算所得项。

计算所得项要放**未赋值的项**的后面。

划分这两者的规则，就像是数学的基础运算一样，靠理解+使用来熟悉吧。

咋样算是常数项（自己总结的，可能不太完美，[中文文档点这里](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Enums.html)）：

1. 第一项，并且未赋值。那么他默认值是0
2. 他未赋值，且前一项是常数项。那么他默认值自动+1（这也就是为啥未赋值的项不能跟在计算所得项的后面了，因为，ts不知道这一项的值应该是啥）
3. （常数项表达式），小括号里面。这样得到的也是个常数
4. 常数项表达式包含一元运算符
5. 包含二元运算符，但是结果不为，NaN || Infinity （这俩会报错）



#### 常数枚举

使用 `const enum` 定义

常数枚举与普通枚举的区别是，它会在编译阶段被删除，并且**不能包含计算成员**。

只存在于写代码这个阶段，避免了不必要的变量创建，消耗内存

常量枚举里的项只能用常量枚举里的别的项初始化，或者常量



#### 外部枚举 Ambient Enums

使用 `declare enum` 定义

`declare` 定义的类型只会用于编译时的检查，编译结果中会被删除。

值和名的映射关系就没有了，用到了，只留有名。

外部枚举与声明语句一样，常出现在声明文件中。



常数枚举和外部枚举一起使用的话，是常数枚举的编译结果。



------



### 类

#### 访问修饰符

TS的三种访问修饰符 (Access Modifiers)

分别是 `public`、`private` 和 `protected`。

public 默认，公开的

private 私有的，只有**声明它**的**类的内部**可以访问。

protected 受保护的，和private区别是，它在子类中也是允许被访问的。



需要注意的是，TypeScript 编译之后的代码中，并没有限制 `private` 属性在外部的可访问性。



#### 抽象类 abstract

抽象类是不允许被实例化的 

抽象类中的抽象方法必须被子类实现

```ts
abstract class Animal {
    public name;
    public constructor(name) {
        this.name = name;
    }
    public abstract sayHi();
}

class Cat extends Animal {
    public sayHi() {
        console.log(`Meow, My name is ${this.name}`);
    }
}

let cat = new Cat('Tom');
```

需要注意的是，即使是抽象方法，TypeScript 的编译结果中，仍然会存在这个类



#### 给类加上类型

与接口类似

```ts
class Animal {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    sayHi(): string{
    		console.log(`My name is ${this.name}`);
    };
}

let cat: Animal = new Cat('Tom');
```



### 类与接口

接口的另一个用途，对类的一部分行为进行抽象。

实现 `implements`

不同的类之间共有的特性提取成接口

一个类可以实现多个接口

```ts
interface Alarm {
    alert();
}

interface Light {
    lightOn();
    lightOff();
}

class Car implements Alarm, Light {
    alert() {
        console.log('Car alert');
    }
    lightOn() {
        console.log('Car light on');
    }
    lightOff() {
        console.log('Car light off');
    }
}
```

`Car` 实现了 `Alarm` 和 `Light` 接口，既能报警，也能开关车灯。

接口与接口之间可以是继承关系 extends

接口也可以继承类



#### 混合类型

一个对象可以同时做为函数和对象使用，并带有额外的属性。

```ts
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number): string { return '' };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```



------



### 泛型 Generics

泛型是指在定义函数、接口或类的时候，不预先指定具体的类型，而在**使用的时候再指定类型的一种特性。**

> *泛型变量*：是一种特殊的变量，只用于表示类型而不是值

![给函数添加类型变量](https://raw.githubusercontent.com/Sunxirui00/yangyang-s-pics/master/2019-06/类型变量.png)

冒号后面的是类型声明。



泛型应用在数组的声明上，如下

```typescript
let fibonacci: Array<number> = [1, 1, 2, 3, 5];
```

指定了变量的值是数组，数组的成员都是数字



看如下的代码： `T` 用来指代任意输入的类型

```ts
function createArray<T>(length: number, value: T): Array<T> {
    let result: T[] = []; // 变量名: 类型+[] 是数组的声明方式，类型也可以是泛型、联合类型
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray<string>(3, 'x'); // ['x', 'x', 'x']
```

* 函数名后添加了 `<T>`
* 输入 `value: T` 
* 输出 `Array<T>` 
* 调用的时候指明`createArray<string>`

也可以不手动指明，类型推论可以自动推算

可以一次定义多个参数类型

```ts
function swap<T, U>(tuple: [T, U]): [U, T] {
    return [tuple[1], tuple[0]];
}

swap([7, 'seven']); // ['seven', 7]
```

输入是个【元组】，调用的时候，根据入参，T代表number，U就代表了string，在定义函数的时候，并没有确定。



#### 泛型约束

我们可以对泛型进行约束

```ts
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}
```

这样就约束了入参，要带有`length`属性

多个类型参数之间也可以互相约束

```ts
function copyFields<T extends U, U>(target: T, source: U): T {}
```



#### 泛型接口

使用含有泛型的接口来定义函数的形状

```ts
interface CreateArrayFunc {
    <T>(length: number, value: T): Array<T>;
}

let creatArray: CreateArrayFunc;
creatArray = function<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i< length; i++){
        result[i] = value;
    }
    return result
};

creatArray(3, 'x');
```

也可以把泛型参数提到接口名上，之后使用泛型接口时，需要定义泛型的类型。

```ts
interface CreateArrayFunc<T> {
    (length: number, value: T): Array<T>;
}

let creatArray: CreateArrayFunc<any>;
```



#### 泛型类

```ts
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```



#### 泛型参数的默认类型

只会在：

1. 代码中直接指定类型参数
2. 实际值参数中也无法推测出

起作用

```ts
function createArray<T = string>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}
```



------



### 声明合并

#### 函数的重载

#### 接口中的属性在合并时会简单的合并到一个接口中

**合并的属性的类型必须是唯一的**，如果重复的字段，类型不同，报错

接口中方法的合并，与函数的合并一样

#### 类的合并与接口的合并规则一致



