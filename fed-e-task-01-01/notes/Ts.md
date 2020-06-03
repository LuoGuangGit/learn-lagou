## TypeScript 概述

* 是一门基于 `JavaScript` 基础之上编程语言
* 重点解决了 `JavaScript` 语言自有类型系统的不足
* 大大提高了代码的可靠程度
* 是一个 `JavaScript` 的超集或者叫扩展集

> 内容概要：
>
> * 强类型与弱类型
> * 静态类型与动态类型
> * `JavaScript` 自有类型系统的问题
> * `Flow` 静态类型检查方案
> * `TypeScript` 语言规范与基本应用

## 类型系统

> <font color=orange>强类型</font> 与 <font color=orange>弱类型</font>（类型安全）
>
> <font color=orange>静态类型</font> 与 <font color=orange>动态类型</font>（类型检查）
>
> 它们分别是从 <font color=orange>**类型安全**</font> 和 <font color=orange>**类型检查**</font> 这两个维度去区分了不同的编程语言

#### 类型安全

* 强类型
  * 定义：在语言层面就限制了函数的实参类型必须与形参类型相同
* 弱类型
  * 在语言层面不会限制实参的类型，即便说函数需要的参数是整型的数字，可是在调用的时候仍然可以传入任意类型的数据，语法上是不会报错的，在运行上有可能会出现问题

> 存在争议：由于这种强弱类型之分根本不是某一个权威机构的定义，所以就导致了后人对这种界定方式的细节出现了一些不一样的理解
>
> 强类型有更强的类型约束，而弱类型中几乎没有什么约束
>
> <font size=2 color=green>***强类型语言中不允许任意的隐式类型转换，而弱类型语言则允许任意的数据隐式类型转换***</font>

#### 类型检查

> 关于静态类型语言与动态类型语言之间的差异并没有什么争议，大家都很统一

* 静态类型

  * 最主要的表现：一个变量声明时它的类型就是明确的，而且在声明过后，它的类型就不允许在修改

* 动态类型

  * 特点：在运行阶段才能明确变量类型，而且变量的类型随时可以改变，例如：

    ```javascript
    var foo = 100 // 当程序运行到这一行才能明确这个 foo 的类型是 number
    foo = 'bar' // 然后再将这个变量的值修改为一个字符串，那这种用法也是被允许的
    console.log(foo)
    ```

  * 也可以说在动态类型语言中它的变量是没有类型的，而变量中所存放的值是有类型的

> 由于 `JavaScript` 是一门 **弱类型** 而且是 **动态类型** 的语言，语言本身的类型系统是非常薄弱的，甚至可以说 `JavaScript` 根本就没有一个 **类型系统*** ，这种语言的特征用一个比较流行的词来说就是 <font color=blue>任性</font>，因为它几乎没有任何的类型限制，所以 `JavaScript` 这门语言也是极其灵活多变的，但是在这种灵活多变的表象背后，丢失掉的就是类型系统的可靠性

#### 为什么 JavaScript 不能设计成一门强类型或者静态类型的这种更靠谱的语言呢？

* 跟 `JavaScript` 的背景有关
  * 在早前根本没想到 `JavaScript` 的应用会发展到今天这种规模；最早的 `JavaScript` 的应用不会太复杂，需求都非常简单，很多时候几百行代码、甚至是几十行代码就搞定了，在这种一眼就能看到头的情况下，类型系统的限制呢就会显得很多余，或者说很麻烦
  * `JavaScript` 是一门脚本语言，脚本语言的特点就是不需要编译就直接在运行环境当中去运行，换句说 `JavaScript` 是没有编译环节的，那即便把它设计成一门静态类型的语言也是没有意义的，因为静态类型语言需要在编译阶段去做检查，而 `JavaScript` 它根本就没有这样的环节

> 在大规模应用下，`JavaScript` 弱类型 / 动态类型 的这种 "优势" 就变成了它的短板

#### 弱类型的问题

* 调用对象当中一个根本不存的方法：在这个对象当中根本不存在这个方法，但是在语言的语法层面这样写是可行的，只是，一旦把这个代码放到环境中去运行就会爆出一个错误，那也就是说在 `JavaScript` 这种弱类型的语言当中，我们就必须要等到运行阶段才能够去发现代码当中的一些类型异常，而且如果这里并不是立即去执行这个方法，例如把它放到一个 `setTimeout` 当中，那么当程序在刚刚启动运行时，还没有办法去发现这个异常，一直等到这行代码执行了，才有可能去抛出这样一个异常，这也就是说如果我们在测试的时候没有测试到这一行代码，那这样一个隐患就被留到我们的代码当中；而如果是强类型的语言的话，那在这里去调用一个不存在的方法，那这里在语法上就会报错，根本就不用等到去运行这行代码

  ```javascript
  const obj = {}
  obj.foo() // 这样写，在语言的语法层面是可行的，在运行时会出先错误
  setTimeout(() => {
      obj.foo() // 这样的写，如果没有测试到这行代码，这样的隐患就会留在代码当中
  }, 100000)
  ```

* 定义一个求和函数，如果在调用的时候传入的参数时数字那么自然没问题，当如果传入的参数时字符串时，函数的返回值就会变成一个拼接的字符串，这就是因为类型不确定所造成的一个最典型的问题

  ``` javascript
  function sum (a, b) {
      return a + b
  }
  console.log(sum(100, 200)) // 传入的是数字，打印：300
  console.log(sum(100, '300')) // 传入了字符串参数，打印结果：'100300'
  ```

* 通过索引器的语法给一个对象添加属性（对象的属性名只能是字符串或者是 `ES6` 所推出的 `Symbol`），但是 `JavaScript` 是弱类型的，所以说可以在索引器中使用任意类型的值去作为属性，在内部会自动转换成字符串

  ```javascript
  const obj = {}
  obj[true] = 100
  console.log(obj['true']) // 这样也能取到值
  ```

#### 强类型的优势

* 错误更早暴露
* 强类型的代码更智能，编码更准确
* 使用强类型语言重构更牢靠
* 强类型的语言会减少不必要的类型判断

## 快速上手

```javascript
// 初始化
// yarn init --yes

// 安装，可以安装到全局，也可以安装到项目，建议安装到项目（考虑到项目依赖）
// yarn add typescript --dev

// 编译
// yarn tsc 01-getting-starting.ts
```

#### TypeScript 配置文件

> 使用 `yarn tsc --init` 生成一个 `tsconfig.json` 的配置文件
>
> 在这个配置文件当中的
>
> * `target` 属性表示转换之后 `Js` 的版本
> * `outDir` 属性用来设置编译结果输出到的文件夹
> * `rootDir` 属性的作用是用来配置源代码所在的文件夹
> * `sourceMap` 属性可以开启源代码映射，这样的话我们在调试的时候就能够使用 `sourceMap` 文件去调试 `TypeScript` 源代码了，非常方便
> * `strict` 属性可以开启关闭严格模式，是否开启所有严格检查选线，在开启情况下对于类型的检查会变得十分严格
> * `lib` 属性指定引用的标准库

#### TypeScript 原始类型

> 绝大多数情况与在 `Flow` 当中是差不多的

```typescript
const a: string = 'string'
const b: number = 100 // NaN Infinity
const c: boolean = true // false
// 与 flow 中不同的是，typescript 中的这三种类型默认是允许为空的（在非严格模式下），也就是说在这里可以为它们赋值一个 null 或者 undefined ，但是在严格模式下会报错

const e: void = undefined // 在严格模式下只能为 undefined 非严格模式下可以为 null
const f: null = null
const g: undefined = undefined

const h: symbol = Symol() // 在这里使用的是用会报错，因为设置的标准库是 ES5 需要在 tsconfig.json 配置文件中在 lib 属性中添加 ES2015 和 DOM
```

#### 中文错误消息

> 在命令行中使用 `yarn tsc --locale zh-CN` 绝大数的提示会变成中文信息

#### TypeScript Object 类型

```typescript
const foo: object = function () {} // [] // {}
// 这里可以赋值函数、数组、对象 object并不单指普通的对象

// 普通对象类型，需要使用类似对象字面量的语法去标记类型
const obj: {foo: number, bar: string} = {foo: 123, bar: 'string'}
```

#### TypeScript 数组类型

```typescript
const arr1: Array<number> = [1, 2, 3]
const arr2: number[] = [1, 2, 3]
```

#### 元组类型

> 是一种特殊的数据结构，元组就是一个明确元素数量以及每个元素类型的数组

#### 枚举类型

使用 `enum` 关键词去声明一个枚举

```typescript
enum PostState {
    Draft = 0,
    Unpublished = 1,
    Published = 2
}
// 在枚举当中如果没有指定值，那么枚举的值会从 0 开始累加，如果给枚举的第一个成员指定了值，那么后面的值都会在这个基础上累加
// 枚举除了可以设置数字，也可以设置字符串，由于字符串是无法像数字那样自增长的，如果是字符串枚举的话，就需要手动的去给每个成员去初始化一个明确值，不太常见
// 枚举类型会入侵到我们运行时的代码，通俗来讲就是它会影响我们编译后的结果， 编译后不会移除，它最终会编译为一个双向的键值对对象（可以通过键去获取值，也可以通过值去获取键）

const post = {
    title: 'hello typescript',
    content: 'typescript is a typed superset of javascript'
    status: PostState.Draft
}
```

#### 函数类型

```typescript
// 函数声明对应的类型限制
function fun1 (a: number, b: number): number {
    return a * b
}
fun1(10, 20) // 参数的个数也必须完全的相同，在参数的名称后面添加一个 ? 这样的话这个参数就变成了一个可选参数，也可以通过给参数设置默认值来达到可填可不填的效果

// 函数表达式对应的类型限制
const fun2: (a: numver, b: number) => number = function (a: numver, b: number) {
    return a * b
}
```

#### 隐式类型推断

> 在 `TypeScript` 当中，如果我们没有明确通过类型注解去标记一个变量的类型，那么 `TypeScript` 会根据这个变量的使用情况去推断这个变量的类型，这样一种特性叫做隐式类型推断

#### 类型断言

```typescript
const nums = [110, 120, 119, 112]
const red = nums.find(i => i > 0)

const num1 = res as number
const num2 = <number>res
// 使用 num1 和 num2 当中的方式明确告诉 typescript 变量的类型是什么
```

#### 接口

> 可以把理解成一种规范或者一种契约，一种抽象的概念
>
> 接口最直观的体现就是用来去约定一个对象当中具体应该有那些成员，而且那些成员的类型是什么样的
>
> 使用 `interface` 关键词去定义接口

```typescript
interface Post {
    title: string // 这里可以使用 , 或者 ; 也可以不用，更标准的语法是使用 ;
    content: string
}
function printPost (post: Post) { // 此时就显式地要求我们所传入的对象必须要有 title 和 content 这两个成员了
    console.log(post.title)
    console.log(post.content)
}
printPost({
    title: 'hello typescript',
    content: 'a javascript superset'
})

// 接口就是用来去约束对象的结构，一个对象去实现一个接口，它就必须要去拥有这个接口当中所约束的所有的成员
```

* 可选成员：在接口的属性名后添加一个 `?` 这就表示这个成员是可有可无的，用法其实就相当于标记它的类型是 `string` 或者 `undefined`
* 只读成员：在接口的属性名前添加 `readonly` 关键词去修饰一下这个成员，添加了 `readonly` 过后，这个成员在初始化完成过后就不能够再去修改了，如果再次去修改就会报错
* 动态成员：在接口当中使用 `[prop: sring]: string ` 去定义动态成员，这样的就可以去动态的添加成员了，只是定义的类型必须是定义好的类型

#### 类 class

> 就是可以用来去描述一类具体事物的抽象特征，可以用来描述一类具体对象的抽象成员
>
> 在 `TypeScript` 中类的属性在使用前必须要先在类型当中去声明，这样做的目的就是为了给属性做一些类型的标注

```typescript
class Person {
    name: string // = 'string' 不在这里设置默认值，那么就必须在构造函数当中去赋值（不能同时在两个地方赋值）
    age: number
    
    constructor (name: string, age: number) {
        this.name = name
        this.age = age
    }
    
    sayHi (msg: string): void {
        console.log(`i am ${this.name}, ${msg}`)
    }
}
```

> 类的访问修饰符：
>
> * `private`：表示为私有成员，那么这中私有属性就只能在类的内部去访问，子类也不能访问
> * `public` 表示为共有成员，在 `TypeScript` 中类成员的访问修饰符就是 `public` ，加不加效果是一样，建议手动去加上这个修饰符，因为这样的话我们的代码会更加容易理解一点
> * `protected` 表示为受保护的成员，指的是只允许在子类当中去访问对应的成员

> 只读属性：`readonly` 如果已经设置了访问修饰符，那么只读属性应该跟着访问修饰符的后面

```typescript
class Person {
    public name: string
    private age: number
    protected readonly gender: boolean
    
    constructor (name: string, age: number) {
        this.name = name
        this.age = age
        this.gender = true
    }
    
    sayHi (msg: string): void {
        console.log(`i am ${this.name}, ${msg}`)
        console.log(this.age)
    }
}

class Student extends Person {
    constructor (name: string, age: number) {
        super(name, age)
        console.log(this.gender)
    }
}

const tom = new Person('Tom', 24)
console.log(tom.name)
```

#### 抽象类

> 使用 `abstract` 关键词定义抽象类，类被定义成抽象类之后，它就只能够被继承，不能够再使用 `new` 的方式去创建对应的实例对象了，那么在这种情况下，我们就必须要使用子类去继承这个类型，在抽象类当中还可以定义一些抽象方法，那这种抽象方法需要使用 `abstract` 关键词来去修饰一下

```typescript
abstract class Animal {
    eat (food: string): void {
        console.log(`呼噜呼噜的吃：${food}`)
    }
    abstract run (distance: number): void
}
class Dog extends Animal {
    run (distance: number): void {
        console.log('四角爬行', distance)
    }
}
const d = new Dog()
d.eat('蛋糕')
d.run(100)
```

#### 泛型

> 就是指我们在定义函数、接口或类的时候，我们没有去指定具体的类型，等到我们使用的时候再去指定具体类型的特征