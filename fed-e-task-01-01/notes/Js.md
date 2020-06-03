## ECMAScript 概述

1. 通常看作 ```JavaScript``` 的标准化规范，实际上 ```JavaScript``` 是 ```ECMAScript``` 的扩展语言
2. 只提供了最基本的语法（约定了代码的编写规范，例如该怎样去定义变量和函数，后者是如何去实现分支循环之类的语句，它只是停留在语言层面，并不能直接拿去完成实际的功能开发，而 ```JavaScript``` 实现了 ```ECMAScript``` 的语言标准，并且在此基础上做了一些扩展，使得我们可以在浏览器环境中操作 ```BOM``` 和 ```DOM``` ，在 ```Node``` 环境中实现 ***读写文件*** 之类的操作）

> 浏览器环境中：```ECMAScript``` + ```Web``` 提供的 ```API``` ( ```BOM``` 和 ```DOM``` )
>
> node环境中：```ECMAScript``` + ```Node``` 提供的 ```API``` ( ```fs``` 和 ```net``` 这样的内置模块 )

<font size=2 color=grey><i>```JavaScript``` 语言本身指的就是 ```ECMAScript```</i></font>



## ES2015（ES6）<font size=2 color=grey><i>可以算是 ```ECMAScript``` 标准的代表版本</i></font>

* 解决原有语法上的一些问题或者不足

  * ```let```  ```const``` 提供的块级作用域

  * 块级作用域

    ...

* 对原有语法进行增强，使其变得更为便捷易用

  + 解构

  + 展开

  + 参数默认值

  + 模板字符串（增强了定义字符串的方式）

    ...

* 全新的对象、全新的方法、全新的功能

  * ```Promise```

  * ```Proxy```

  * ```Object.assign()```

    ...

* 全新的数据类型和数据结构

  * ```Symbol```

  * ```Set```

  * ```Map```

    ...

#### 一. 作用域

>  <font size=2 color=grey><i>某个成员能够起作用的范围</i></font>
>
> 在 ```ES2015``` 之前，在 ```ESMAScript``` 中只用两种作用域
>
> * 全局作用域
> * 函数作用域
>
> 在 ```ES2015``` 中新增 <font color=red>**块级作用域**</font>

块级作用指的是在代码中用一对 <font color=red>```{}```</font> 包裹起来的范围，例如：

```javascript
// if 语句和 for 语句中的 {} 都会产生‘块’
if (true) {
    console.log('zce.me')
}

for (var i = 0; i < 10; i++) {
    cosole.log('zce.me')
}
//-----------------------------------
// 以前 ‘快’ 是没有独立的作用域的，这会导致 ‘块’ 中定义的成员在外部也能访问到
if (true) {
    var foo = 'zce'
}
console.log(foo) // 打印 zce
// 这点对于复杂代码是非常不利的，也是不安全的
```

##### 变量声明

> ```var``` 和  ```let``` ```const``` 的区别：
>
> 1. ```let```
>
>    * 只能在所声明的代码块中被访问
>    * <font color=red>不允许</font> 在相同作用域内 <font color=red>重复声明</font>
>    * 不存在 <font color=red>变量提升</font>，必须遵循 <font color=red>先声明，在使用</font> 否则就会报错
>
> 2. ```const```
>
>    * <font color=red>拥有 ```let``` 的所有特性</font>
>
>    * 只读：声明过后不允许再被修改（是指不允许在定义赋值后，重新去指向一个新的内存地址，并不是不允许修改 <font color=red>常量</font> 中的属性成员），例如：
>
>      ```javascript
>      const obj = {}
>      obj.name = 'luoguang' // 这种情况实际上并没有修改 obj 所指向的内存地址，它只是修改了这个内存空间当中的数据，所以说是被允许的
>      obj = {} // 会改变 obj 的内存指向，所以这样会报错
>      ```

```let``` 的使用：

```javascript
var elements = [{}, {}, {}]
for (var i = 0; i < elements.length; i++) {
    elements[i].onclick = function () {
        console.log(i)
    }
}
elements[2].onclick() // 打印 elements 的长度，即：3
// 这是因为这里打印的 i 它始终都是全局作用域中的 i 在循环执行完后就已经被累加到了 3 所以无论是执行那个元素的click 打印出的结果都是 3

// 闭包解决
var eles = [{}, {}, {}]
for (var j = 0; j < eles.length; j++) {
    eles[j].onclick = (function (j) {
        return function () {
            console.log(j)
        }
    })(j)
}
eles[2].onclick() // 打印 2

// 使用let -----------------------------------------------
var elems = [{}, {}, {}]
for (let i = 0; i < elems.length; i++) {
    elems[i].onclick = function () {
        console.log(i)
    }
}
elems[2].onclick() // 正确打印内容：2
```

> <font size=2 color=grey><i>**闭包：**借助函数作用域来摆脱全局作用域的影响</i></font>

在 ```for``` 中会有两层作用域：

```javascript
for (let i = 0; i < 3; i++) {
    let i = 'foo' // 内层独立的作用域中定义的变量
    console.log(i) // 打印三次 foo 说明这里声明的两个 i 是互不影响的，也就是说它们不会在同一个作用域中
}
// 使用 if 判断拆分 for 循环 ------------------------------
let i = 0
if (i < 3) {
    let i = 'foo'
    console.log(i) // 打印 foo
}
i++
if (i < 3) {
    let i = 'foo'
    console.log(i) // 打印 foo
}
i++
if (i < 3) {
    let i = 'foo'
    console.log(i) // 打印 foo
}
i++
// 代码拆解过后可以看出 let i = 'foo' 它实际上是 if 块级作用域内部的一个变量，而外部的‘循环计数器’（let i = 0）实际上外部循环的这个‘块‘里面所产生的变量，所以它们是互不影响的（两层嵌套的作用域，循环体中的 i 是内层独立的作用域，外层是 for 循环本身的作用域）
```

<font color=green>最佳实践：不使用 ```var``` ，主用 ```const``` 配合 ```let``` (需要修改的值)</font>

#### 解构

<font color=orange>从数组或对象中获取指定元素的快捷方式</font>

<font color=red>数组的解构</font>：

* 将之前定义变量的地方修改 ```[]``` 那么 ```[]``` 里面就是我们需要提取出来的数据所存放的变量名，那么内部就会按照 ```[]``` 中变量出现位置分配数组当中多对应位置的数值，如：```const [n1, n2, n3] = [100, 200, 300]``` 
* 如果只需要获取其中某个位置的成员，例如获取第三个成员：```const [, , n3] = [100, 200, 300]```（确保 <font color=red>解构位置</font> 的 <font color=red>格式</font> 与数组是 <font color=red>一致</font> 的，这样的话就能提取到指定位置的成员）；除此之外还可以在 <font color=red>解构位置的变量名</font> <font color=red>**之前**</font> 去添加 ```...``` 表示提取从当前位置开始往后的所有成员，最终所有的结果会放到一个数组当中，注意：这种方式只能在 <font color=red>解构位置</font> 的 <font color=red>最后一个成员</font> 上使用
* 如果 <font color=red>解构位置</font> 的成员个数 <font color=red>小于</font> 被解构的数组长度，那么就会按照 <font color=red>从前到后</font> 的顺序去提取，那么多出来的成员就不会被提取，反之，如果 <font color=red>解构位置</font> 的成员个数 <font color=red>大于</font> 被解构的数组长度，那么提取到就是 ```undefined```
* 如果需要给提取到的成员设置默认值，只需要在 <font color=red>解构位置的变量名</font> <font color=red>**之后 **</font> 跟上一个 ```=``` 然后写上默认值，这样的话如果没有从数组中提取到对应的成员，那么这里的变量就会得到设置的默认值，如：```const [n1, n2, n3, n4 = 123, s5 = 'default value'] = [100, 200, 300]```

```javascript
// es2015 之前
var arr = [100, 200, 300]
var num1 = arr[0]
var num2 = arr[1]
var num3 = arr[2]
console.log(num1, num2, num3) // 打印 100, 200, 300

// es2015 中使用解构快速提取数组当中的指定成员 --------------------
const [n1, n2, n3] = [100, 200, 300]
console.log(n1, n2, n3) // 打印 100, 200, 300

const [ , , nu3] = [100, 200, 300]
connsole.log(nu3) // 打印 300

const [numb1, ...rest] = [100, 200, 300] // ... 只能在最后使用
console.log(rest) // 打印 [200, 300]

const [n1, n2, n3, n4 = 123, s5 = 'default value'] = [100, 200, 300]
console.log(n4, s5) // 打印 123 'default value'
```

<font color=red>对象的解构</font>：

* 需要根据 <font color=red>属性名</font> 去匹配提取，而不是位置，其它特点基本与 <font color=red>数组的解构</font> 是完全一致，<font color=red>解构位置</font> 使用 ```{}```
* 因为解构的变量名它同时又是用来匹配被解构对象当中的属性名的，所以在当前作用域中有同名的成员就会产生冲突，解决方式--重命名：```const { name: objName } = { name: 'luoguang' }```

```javascript
const obj = { name: 'luoguang', age:24 }
const { name } = obj
console.log(name) // 打印 'luoguang'

// 在当前作用域中有同名成员
const name = 'tom'
const { name: objName = 'jack' } = { name: 'luoguang', age:24 }
```

#### 模板字符串

* 使用反引号 ``` `` ``` 来标识

* 可以换行使用（传统字符串不支持换行，换行需要使用换行符）；对于输出 ```html``` 字符串是非常方便的，这种方式会比之前使用字符串拼接的方式要方便得多，也更直观一点，不容易写错

*  支持通过插值表达式的方式在字符串中嵌入所对应的数值 ``` `${name}` ```

* ``` `${name}` ``` 里 ```{}``` 中的内容就是标准的 ```JavaScript``` ，也就是说这里不仅可以嵌入变量，还可以嵌入任何标准的 ```js``` 语句，例如：``` `${1 + 2}` ``` 和 ``` `${Math.random()}` ```，这个语句的返回值最终会被输出到字符串当中插值表达式所对应的位置

* <font color=red>标签函数：</font>

  * 在使用模板字符串之前使用一个标签，这个标签实际上是一个特殊的函数，添加这个函数就是调用这个函数，如：```const str = console.log`${Math.random()}` ``` 

  * 定义的标签函数可以接收到一个数组参数，这个数组当中的值就是模板字符串中的内容分割过后的结果，这是因为在模板字符串当中可能会有嵌入的表达式，所以这里的数组实际上就是按照表达式分割过后那些静态的内容

  * 除了可以接收到数组以后，在标签函数中还可以接收到所有在模板字符串当中出现的表达式的返回值

  * 函数内部的返回值就会是这个带标签的模板字符串所对应的返回值，例如在函数中 ```return '123'``` 那么对应的这个带标签的模板字符串的值就是 ```123```

    > 作用：对模板字符串进行加工
    >
    > * 文本的多语言化，中英文
    > * 检查模板字符串中是否存在一些不安全的字符之类的需求
    > * 甚至可以用来实现一个小型的模板引擎

```javascript
const name = 'Tom'
const msg = `hi, ${name}`
console.log(msg) // 打印： hi, Tom

// 模板字符串标签函数
const name = 'Tom'
const gender = true
function myTagFunc (strings, name, gender) {
    const sex = gender ? 'man' : 'woman' // 通过 gender 来判断性别，使其更适合用户阅读
    console.log(strings) // 打印：['hey, ', ' is a ', '.']
    console.log(name, gender) // 打印：Tom true
    return strings[0] + name + strings[1] + sex + strings[2]
}
const result = myTagFunc`hey, ${name} is a ${gender}.`
console.log(result) // 如果在函数中没有返回内容，那么这里就会打印出 undefined 有返回值的话，那么这里打印的就是函数所 return 的值
```

#### 字符串的扩展方法

* ```includes()```：判断字符串中是否包含指定字符串，返回 ```true``` 或 ```false```
* ```startsWith()```：判断字符串是否是以指定字符开头的，返回 ```true``` 或 ```false```
* ```endsWith()```：判断字符串是否是以为指定字符结尾的，返回 ```true``` 或 ```false```

> 它们是一组方法，可以用来更方便的去判断字符串当中是否包含指定的内容，返回值是 ```true``` 或 ```false```
>
> 相比于之前使用 ```indexOf()``` 或者使用正则去判断，这样一组方法会让我们字符串查找便捷很多

```javascript
const msg = 'Error: foo is not defined.'
// 判断字符串是否是以某个字符开头
console.log(msg.startsWith('Error')) // 打印：true
// 判断字符串是否是以某个字符结尾
console.log(msg.endsWith('.')) // 打印：true
// 判断字符串中是否包含某个字符
console.log(msg.includes('foo')) // 打印：true
```

#### 函数参数

* 参数默认值：

  * 在 ```ES2015``` 之前如果需要给函数中参数设置默认值，需要在函数体中通过逻辑代码来实现，例如：

    ```javascript
    function foo (enable) {
        enable = enable || true // 通过判断是否有传递参数设置默认值
        console.log('foo invoked - enable:')
        console.log(enable)
    }
    foo()
    foo(false) // 在当前情况下其实并不适合使用短路表达式的方式来设置默认值，因为如果传递的参数是 false 也会使用默认这，正确做法：enable = enable === undefined ? true : false
    ```

  * ```ES6``` 中定义函数参数默认值：在形参后面通过 ```=``` 来设置默认值，这里所设置的默认值只会在没有传递实参，或者实参传递的是个 ```undefined``` 的时候才会被使用

    > <font color=red>**注意：**</font>如果有多个参数，那么需要设置默认值的参数一定要出现在参数列表的最后，因为参数是按照次序传递的，如果带有默认值的参数不在最后的话，那么我们的默认值将无法正常工作

    ```javascript
    function foo (enable = true) {
        console.log(enable)
    }
    foo() // 在这种情况下才会使用参数的默认值
    foo(false) // 在这种情况会使用传递的参数
    ```

+ 剩余参数：

  + 在 ```ES2015``` 之前接收未知个数的参数是使用 ```ECMAScript``` 提供的 ```arguments``` 对象去接收的，```arguments``` 对象实际上是个伪数组 

  + ```ES6``` 中接收未知个数的形参：在形参前面加上 ```...``` ，那么此时这个形参就会以数组的形式去接收从当前参数位置往后所有的实参

    > <font color=red>**注意：**</font>因为接收的是位置个数的参数，所以这种操作符只能出现在形参的最后一位，而且只能够使用一次

```javascript
// before es2015
function foo () {
    console.log(arguments) // 打印：[Arguments] { '0': 1, '1': 2, '2': 3, '3': 4 }
}
foo(1, 2, 3, 4)
// after es2015
function bar (...args) {
    console.log(args) // 打印：[ 1, 2, 3, 4 ]
}
bar(1, 2, 3, 4)
```

#### 数组展开

使用 ```...``` 将数组依次展开，例如：

```javascript
const arr = ['foo', 'bar', 'baz']
// before es2015
console.log.apply(cosole, arr) // 打印：foo bar baz
// after es2015
console.log(...arr) // 打印：foo bar baz
```

#### 箭头函数

* 简化了函数的定义，语法：```const fn = n => n + 1 ```，其中 ```=>``` 左边实际上就是参数列表，如果需要使用多个参数，那么可以使用 ```(n, m)``` 这样的方式定义，```=>``` 右边是函数体 ```n + 1```，在这里只有一句表达式，那么它的执行结果就会作为函数的返回值返回，如果在这个函数体中需要执行多条语句，那么可以使用 ```{}``` 去包裹，不过使用了这种方式之后，那么函数的返回值需要手动通过 ```return``` 关键字去返回，如果只是返回一个对象，可以使用 ```({ name: 'luoguang' })``` 这种方法返回
* 箭头函数不会改变 ```this``` 指向

#### 对象

* 对象字面量的增强，传统的对象字面量要求我们必须要在 ```{}``` 中使用 **属性名** ```:``` **属性值** 这种语法，即便对象属性的值是一个变量，那么也必须是 **属性名** ```:``` **变量名** 这种语法，而在 ```ES6``` 中如果变量名与我们要添加到对象当中的属性名是一致的话，那么就可以省略 ```:``` 和后面的 **变量名** ，如：

  ```javascript
  // before es2015
  const foo = 123
  const obje = {
      bar: '456',
      foo: foo
  }
  
  // after es2015
  const bar = '123'
  const obj = {
      foo: 123,
      bar
  }
  ```

* 在对象中添加函数时，可以省略掉 ```:``` 和后面的 ```function``` ，如：

  ```javascript
  // before es2015
  const obj1 = {
      foo: 123,
      method1: function () {}
  }
  
  // after es2015
  const obj = {
      foo: '123',
      method1 () {} // 这种方式定义的函数其实就是一个普通的函数，所以在函数体中使用 this 指向和传统定义的函数的指向是一样的
  }
  ```

* 计算属性名：在 ```ES6``` 中可以使用表达式的返回值作为对象的属性名，以前如果需要给对象添加一个动态的属性名，只能在对象声明过后，通过索引器的方式，也就是 ```[]``` 的方式添加；在 ```ES2015``` 之后对象字面量的属性名就可以通过 ```[]``` 直接去使用动态的值了，这样的特性叫做 <font color=red>**计算属性名**</font>

  ```javascript
  // before es2015
  const obj1 = {
      foo: 123
  }
  obj1[Math.random()]: 456
  
  // after es2015
  const obj2 = {
      foo: 123，
      [Math.random()]: 456
  }
  ```

* 对象扩展方法：

  * ```Object.assign()```：可以将多个源对象中的属性复制到一个目标对象中，如果对象之间有相同的属性，那么源对象中的属性会覆盖掉目标对象中的属性（从源对象中取，往目标对象中放），支持传入任意个数的对象，其中第一参数就是 **目标对象** ，也就是说所有的源对象当中的属性都会复制到这个 **目标对象** 当中，这个方法的返回值就是这个 **目标对象**

    ```javascript
    const source1 = {
        a: 123,
        b: 234
    }
    const target = {
        a: 456,
        c: 789
    }
    // 依次将源对象中的属性复制到目标对象当中，有相同的属性就会覆盖
    const result = Object.assign(target, source1)
    console.log(target) // 打印：{ a: 123, c: 789, b: 234 }
    console.log(result) // 打印：{ a: 123, c: 789, b: 234 }
    console.log(target === result) // 打印：true
    ```

  * ```Object.is()```：比较两个值是否相等

    ```javascript
    // before es2015
    console.log(0 == false) // == 只比较值是否相等，不比较类型，打印：true
    console.log(0 === false) // === 同时比较值和类型是否相同，打印：false
    console.log(+0 === -0) // 无法区分 +0 和 -0，打印：true
    console.log(NaN === NaN) // 打印：false
    
    // after es2015
    console.log(Object.is(+0, -0)) // 打印：false
    console.log(Object.is(NaN, NaN)) // 打印：true
    ```

#### Proxy 代理对象

监视某个对象中的属性读写：

* ```ES5``` 当中通过 ```Object.defineProperty``` 这个方法来为我们的对象添加属性，这样的话，我们就可以捕获到对象当中的读写过程（```vue3``` 之前的版本就是使用的这个方法来实现的数据响应，从而完成双向数据绑定）

* 在 ```ES2015``` 当中全新设计了 ```Proxy``` 的类型，它就是专门用来为对象设置访问代理器的（无论是读取数据还是写入数据都需要经过这样的代理），通过它可以轻松监视到对象的读写过程

* ```Proxy``` 相比于 ```Object.defineProperty``` ，```Proxy``` 的功能要更为强大，使用起来也更为方便

* ```Proxy``` 语法：```new Proxy(object, {})```，```Proxy``` 的构造函数的第一个参数，即 ```object``` 就是需要代理的目标对象，第二个参数也是一个对象，可以把这样一个对象称之为 **代理的处理对象** ，在这个对象中，可以通过 ```get``` 的方法来监视属性的访问，通过 ```set``` 的方法来对象当中设置属性的过程：

  ```javascript
  const person = {
      name: 'luoguang',
      age: 24
  }
  const personProxy = new Proxy(person, {
      get (target, property) {
          // 可以接收两个参数，第一个是所代理的目标对象，第二个就是外部所访问的属性名
          console.log(target, property) // 打印：{ name: 'luoguang', age: 24 } 'name'
          // return 100
          return property in target ? target[property] : 'default'
          // 这个方法的返回值将会作为外部去访问这个属性所得到的结果
      },
      set (target, property, value) {
          // 这个方法默认接收三个参数，分别是：代理的目标对象、要写入的属性名称、以及要写入的属性值
          // 校验代码
          target[property] = value
          console.log(target, property, value)
          // 打印：{ name: 'luoguang', age: 24 } 'gender' true
      }
  })
  console.log(personProxy.name) // 打印：100
  personProxy.gender = true
  ```

* ```Proxy``` 对比 ```definedProperty```：

  * ```Proxy``` 要更为强大一些：```defineProperty``` 只能监视属性的读写，```Proxy``` 能监视到更多的对象操作，比如 ```delete``` 操作或者是对对象当中的方法调用等等

    ```javascript
    const person = {
        name: 'luoguang',
        age: 24
    }
    const personProxy = new Proxy(person, {
        deleteProperty (target, property) { // 代理方法
            // 这个方法会在外部对当前这个代理对象进行 delete 操作时会自动执行，接收两个参数，分别是：代理目标对象以及所要删除的这个属性的属性名称
            console.log('delete', property) // 打印：delete age
            delete target[property]
        }
    })
    delete personProxy.age
    console.log(person) // 打印：{ name: 'luoguang' }
    ```

  * ```Proxy``` 更好的支持数组对象的监视，以往想要通过 ```Object.defineProperty``` 去监视数组的操作，最常见的方式就是通过 **重写数组的操作方法** （大体思路就是：通过自定义的方法去覆盖掉数组原型对象上的 ```push()``` 或者 ```shift()``` 之类的一些的方法，以此来劫持对应的方法调用的过程）

    ```javascript
    const list = []
    const listProxy = new Proxy(list, {
        set (target, property, value) {
            console.log(target, property, value) // 打印：[] '0' 100，分别是目标对象，下标，添加的值
            target[property] = value
            return true
        }
    })
    listProxy.push(100) // proxy 内部会自动根据 push 操作推算出来它应该所处的下标
    ```

  * ```Proxy``` 是以非入侵的方法监管了对象的读写（也就是说一个已经定义好的对象，我们不需要对对象本身去做任何的操作就可以监视它内部成员的读写），而 ```Object.defineProperty``` 的方式就要求我们必须要通过特定的方式单独去定义对象当中那些需要被监视的属性，那么对于一个已经存在的对象，我们想要去监视它的属性，我们需要去做很多额外的操作

    ```javascript
    const person = {}
    Object.defineProperty(person, 'name', {
        get () {
            console.log('name被访问')
            return person._name
        },
        set (value) {
            console.log('name被设置')
            person._name = value
        }
    })
    Object.defineProperty(person, 'age', {
        get () {
            console.log('age被访问')
            return person._age
        },
        set (value) {
            console.log('age被设置')
            person._age = value
        }
    })
    person.name = 'jack'
    console.log(person.name)
    
    // es2015
    const person2 = {}
    const personProxy = new Proxy(person2, {
        get (target, property) {
            return target[property]
        },
        set (target, property, value) {
            target[property] = value
        }
    })
    personProxy.name = 'jack'
    console.log(personProxy.name)
    ```

#### Reflect

> 是 ```ES2015``` 中提供的一个全新的内置对象（提供了一套统一的用于对象操作的 ```API```），如果按照```Java``` 语言的说法 ```Reflect``` 是一个静态类，也就是说它不能通过 ~~```new Reflect()```~~ 去构建一个实例对象，只能够去调用这个静态类当中的一些静态方法（与 ```Math``` 一样）

```Reflect``` 内部封装了一系列对对象的底层操作：

* ```Reflect``` 的 **13** 个方法的方法名与 ```Proxy``` 对象当中的那个处理对象当中的方法名是完全一致的，其实这些方法就是 ```Proxy``` 处理对象当中那些方法的默认实现
  * ```Reflect.has()```：判断对象当中是否存在某一个属性，接收两个参数，分别是：目标对象以及属性名
  * ```Reflect.deleteProperty()```：删除对象当中的某一个属性
  * ```Reflect.ownKeys()```：获取对象当中所有的属性名

#### Promise

> 提供了一种更优的异步编程解决方法，通过链式调用的方法解决了传统异步编程当中回调函数嵌套过深的问题

#### class 类

使用 ```class``` 关键词定义类：

```javascript
class Person {
    constructor (name) { // 当前这个类型的构造函数
        this.name = name
    }
    say () { // 定义实例方法
        console.log(`hi, my name is ${this.name}`)
    }
    static create (name) { // 静态方法通过 static 关键词去定义
        return new Person(name)
    }
}
const p = new Person('luoguang')
p.say()

const t = Person.create('Tom')
t.say()
```

> <font color=red>**注意：**</font>因为静态方法是挂载到类型上面的，所以在静态方法内部，它的 ```this``` 就不会去指向某一个实例对象，而是当前的类型

类的继承：通过继承这种特性，我们就能够抽象出来相似类型之间重复的地方；在 ```ES2015``` 中通过 ```extends``` 关键词实现继承，语法：

```javascript
class Person {
    constructor (name) {
        this.name = name
    }
    say() {
        console.log(`hi, my name is ${this.name}`)
    }
}

class Student extends Person {
    constructor (name, number) {
        super(name)
        this.number = number
    }
    hello () {
        super.say() // 实现父类的 say 方法
        console.log(`my school number is ${this.number}`)
    }
}
const s = new Student('jack', 100)
s.hello()
```

#### Set 数据结构

> 可以理解为集合，它与传统的数组非常类似，不过 ```Set``` 内部的成员是不允许重复的，也就是说每一个值在同一个 ```Set``` 当中都是唯一的，最常见的使用场景就是为数组当中的元素去重

```javascript
const s = new Set() // 它是一个类型，通过这个类型构造的实例就可以用来存放不重复的数据
s.add(1) // 通过这个实例的 add 方法去往集合当中添加一些数据，由于 add 方法会返回集合对象本身，所以我们可以链式调用：s.add(1).add(2).add(3)，如果在这个过程中添加了之前已经存在的值，那么所添加的这个值就会被忽略掉

// 需要遍历集合当中的数据，我们就可以使用集合对象的 forEach 方法 或者使用 es2015 当中提供的 for of 循环
s.forEach(i => console.log(i))
for (let i of s) {
    console.log(i)
}

// 在 Set 对象当中，可以通过 size 属性来获取整个集合的长度，这与数组当中的 length 是相同的道理
console.log(s.size)

// Set 提供了一些常用的方法
console.log(s.has(1)) // has() 可以用来判断集合当中是否存在某一个特定的值，返回 true 或者 false
console.log(s.delete(3)) // delete() 用来删除集合当中某一个指定的值，删除成功会返回 true
s.clear() // clear() 可以用来清除当前集合当中的全部内容
console.log(s.size) // 打印：0

// 去重
const arr = [1, 2, 1, 3, 4]
const result = Array.from(new Set(arr)) // const result = [...new Set(arr)]
console.log(result)
```

#### Map 数据结构

> 它与 ```ECMAScript``` 当中的对象非常类似，本质上它们都是键值对集合，但是这种对象结构中的键只能够是字符串类型，所以我们去存储一些复杂数据时会有一些问题，```Map``` 与对象最大的区别就是 ```Map``` 可以使用任意类型的数据做为键，而对象实际上只能使用字符串作为键

```javascript
const obj = {}
obj[true] = 'value'
obj[123] = 'num'
obj[{ a: 1 }] = 'obj'

console.log(Object.keys(obj))
// 打印：[ '123', 'true', '[object Object]' ]
// 原本设置的布尔值、数字和对象类型的键在这都被转化为了字符串，也就是说如果给我们给对象添加的键不是字符串，那么内部就会将这个数据 toString() 的结果作为键
// 如果需要用到对象作为键，那每一个对象 toString() 的结果默认都是一样的，那么自然就没有办法去做到区分


// 在 es2015 中的 Map 结构就是为了解决这样的问题；Map 才能算是严格意义上的键值对集合，用来去映射两个任意类型数据之间的对应关系，用法：
const m = new Map()
const tom = { name: 'tom' }
m.set(tom, 90)
console.log(m) // 打印：Map { { name: 'tom' } => 90 }
console.log(m.get(tom)) // 获取数据，打印：90
console.log(m.has(tom)) // 判断某一个键是否存在，打印：true
console.log(m.delete(tom)) // 删除某一个键，打印：true
console.log(m) // 打印：Map {}
m.set(tom, 100)
m.clear() // 清空所有的键值
console.log(m) // 打印：Map {}
// 遍历
m.set(tom, 200)
m.set({ b: 2 }, 300)
m.forEach((value, key) => {
    console.log(value, key)
})
```

#### Symbol

>  一种全新的原始数据类型：最主要的作用就是为对象添加独一无二的属性名
>
> 在 ```ES2015``` 以前，对象的属性名都是字符串，而字符串是有可能重复的，如果重复的话就会产生冲突
>
> 从 ```ES2015``` 开始，对象就可以直接去使用 ```Symbol``` 类型的值作为属性名，也就是说现在对象的属性名可以是两种类型，分别是 ```String``` 和 ```Symbol```

```javascript
// 通过 Symbol 函数创建的每一个值都是唯一的，它永远不会重复
console.log(Symbol() === Symbol()) // 打印：false
// 考虑到在开发过程中的调试 Symbol() 函数允许我们传入一个字符串，作为这个值的描述文本
console.log(Symbol('foo')) // 打印：Symbol(foo)
console.log(Symbol('bar')) // 打印：Symbol(bar)

const obj = {
    [Symbol()]: 123
}
console.log(obj) // 打印：{ [Symbol()]: 123 }

// 借助于这种类型的特点，去模拟实现对象的私有成员
const name = Symbol()
const person = {
    [name]: 'jack',
    say () {
        console.log(this[name])
    }
}
```

#### for...0f 循环

> 以后会作为遍历所有数据结构的统一方式

```javascript
const arr = [100, 200, 300, 400]
for (let item of arr) {
    console.log(item) // 打印：100 200 300 400
}
const s = new Set(['foo', 'bar'])
for (let item of s) {
    console.log(item) // 打印：foo bar
}
// 遍历数组和遍历 Set 没有什么区别，它每次迭代拿到也就是当前元素本身

const m = new Map()
m.set('a', 1)
m.set('b', 2)
m.set('c', 3)
for (let [key, value] of m) {
    // 因为在 Map 中每次迭代拿到的是一个包含键和值的数组，所以使用解构语法
    console.log(key, value) // 打印：a 1, b 2, c 3
}

// 对于普通对象，如果我们直接去遍历它，就会爆出一个错误
```

#### 可迭代接口

> ```for...of``` 循环是一种数据统一遍历方式：对于普通对象，如果我们直接去遍历它，就会爆出一个错误
>
> ```ES``` 中能够表示有结构的数据类型越来越多，为了给各种各样的数据结构提供统一的遍历方式，```Es2015``` 中就提出了一个叫做 ```Iterable``` 的接口：意思就是可迭代的（可以理解为一种规格标准），可迭代接口就是一种可以被 ```for...of``` 循环统一遍历访问的规格标准，只要这种数据结构实现了可迭代接口，那么它就能够被 ```for...of``` 循环遍历（也就是它内部必须要挂载一个 ```Symbol.iterator``` 方法，才能被 ```for...of``` 遍历）

实现可迭代接口（```Iterable```）：

```javascript
const obj = {
    store: ['foo', 'bar', 'baz'],
    [Symbol.iterator]: function () {
        const self = this
        let index = 0
        return {
            next: function () {
                const result = {
                    value: self.store[index],
                    done: index >= self.store.length
                }
                index++
                return result
            }
        }
    }
}
for (let item of obj) {
    console.log(item) // 依次打印：foo bar baz
}
// 迭代器模式：
// 对外提供统一遍历的接口，让外部不用再去关心这个数据的内部结构是怎样的
```

#### 生成器 - Generator

> 引入这样一个新特性的目的就是：为了能够在复杂的异步代码中减少回调函数嵌套产生的问题

语法及基本应用：

* 定义生成器函数 - 通过在普通函数的 ```function``` 关键词后面添加 ```*``` 的方式

* 在这样一个对象的原型上也会有一个跟迭代器对象一样的 ```next``` 方法

* 生成器函数也实现了 ```iterator``` 接口，也就是迭代器接口的协议

* 最大的特点：惰性执行

  ```javascript
  function * foo () {
      console.log('zce')
      return 100
  }
  const result = foo()
  console.log(result) // 并没有像普通函数那样打印 zce 和 100 ，而只是打印了一个生成器对象：Object [Generator] {}zce
  console.log(result.next()) // 打印 zce { value: 100, done: true }
  
  // 配合 yield 使用
  function * bar () {
      console.log('111')
      yield 100
      console.log('222')
      yield 200
      console.log('333')
      yield 300
  }
  const generator = bar()
  console.log(generator.next()) // 依次打印：111、{ value: 100, done: false }
  console.log(generator.next()) // 依次打印：222、{ value: 300, done: false }
  console.log(generator.next()) // 依次打印：333、{ value: 300, done: false }
  // 总结：生成器函数它会自动帮我们返回一个生成器对象，当调用这个对象的 next 方法才会让这个函数的函数体开始执行，在执行过程中一旦遇到 yield 关键词，那么函数的执行就会被暂停下来，而且 yield 后面的值将会作为 next 的结果返回，如果再次调用这个生成器对象的 next 那么函数就会从暂停的位置继续执行，周而复始，一直到这个函数完全结束，这样的话那 next 所返回的 done 值也就变成了 true
  
  // 如果在 next 方法中传入参数，那么这个传入的值会赋值给生成器中对应 yield 位置的左边 
  // 如：const users = yield 100 next 中传入的值将会赋值给 users
  ```

* 基本应用：

  ```javascript
  // 发号器：
  function * createIdMaker () {
      let id = 1
      while (true) {
          yield id++
      }
  }
  const idMaker = createIdMaker()
  console.log(idMaker.next().value) // 每次执行 next 可以拿到一个自增的 id
  
  // 实现对象的 iterator 方法
  const todos = {
      life: ['吃饭', '睡觉', '打豆豆'],
      learn: ['数学', '英语', '语文'],
      work: ['喝茶'],
      [Symbol.iterator]: function * () {
          const all = [...this.life, ...this.learn, ...this.work]
          for (const item of all) {
              yield item
          }
      }
  }
  for (const item of todos) {
      console.log(item) // 依次打印：all 数组中的所有项
  }
  ```

## ES2016

> 发布于2016年的6月，与 ```ES2015``` 相比，```ES2016``` 只是一个小版本，仅包含两个小功能：
>
> * ```Array.prototype.includes```：检查数组当中是否包含指定元素
>
>   ```javascript
>   // before ES2016 查找数组当中是否包含指定值使用：indexOf()
>   const arr = ['foo', 1, NaN, false]
>   console.log(arr.indexOf('foo')) // 查找到元素会返回元素所在的下标，没有查找到返回 -1
>   // 这种方式有一个问题，它不能用于去查找数组当中的 NaN
>   
>   console.log(arr.includes('foo')) // 返回 true 或 false，可以用来查找 NaN
>   ```
>
> * 指数运算符：
>
>   ```javascript
>   // before es2016
>   console.log(Math.pow(2, 10))
>   
>   // after es2016
>   console.log(2 ** 10)
>   ```

## ES2017

> 发布于2017年的6月，与 ```ES2015``` 相比，```ES2017``` 也只是一个小版本，新功能：
>
> * ```Object.values()```：返回指定对象当中所有值组成的数组
>
>   ```javascript
>   const obj = {
>       foo: 'value1',
>       bar: 'value2'
>   }
>   console.log(Object.values(obj)) // 打印：[ 'value1', 'value2' ] 
>   ```
>
>   
>
> * ```Object.entries()```：是以数组的形式去返回对象当中所有的键值对，这使得我们可以直接去使用 ```for...of``` 去遍历我们的普通对象
>
>   ```javascript
>   const obj = {
>       foo: 'value1',
>       bar: 'value2'
>   }
>   console.log(Object.entries(obj)) // 打印：[ [ 'foo', 'value1' ], [ 'bar', 'value2' ] ]
>   
>   for (let [key, value] of Object.entries(obj)) {
>       console.log(key, value) // 依次打印：foo value1, bar value2
>   }
>   ```
>
> * ```Object.getOwnPropertyDescriptors()```：实际上是用来帮我们获取对象当中属性的完整描述信息，自从 ```ES5``` 过后，我们就可以为对象去定义 ```get``` 或者 ```set``` 属性，那这种 ```get``` 和 ```set``` 属性是不能通过 ```Object.assign()``` 方法去完全复制的，例如：
>
>   ```javascript
>   const p1 = {
>       firstName: 'Lei',
>       lastName: 'wang',
>       get fullName () { // 相当于给外界提供了一个只读属性
>           return this.firstName + ' ' + this.lastName
>       }
>   }
>   console.log(p1.fullName) // 打印：Lei wang
>   
>   const p2 = Object.assign({}, p1)
>   p2.firstName = 'zce'
>   console.log(p2.fullName) // 打印错误：Lei wang，正确应该打印：zce wang
>   // 这是因为 Object.assign() 在复制时，它只是把 fullName 当作一个普通的属性去复制了，所以才会出现这种情况
>   
>   // 解决方法
>   const descriptors = Object.getOwnPropertyDescriptors(p1)
>   // console.log(descriptors)
>   const p3 = Object.defineProperties({}, descriptors)
>   p3.firstName = 'zce'
>   console.log(p3.fullName) // 打印：zce wang
>   ```
>
> * ```String.propotype.padStart / String.prototype.padEnd```：可以给数字前面添加 0 或者用这种方法对齐输出的字符串长度
>
>   ```javascript
>   const books = {
>       html: 5,
>       css: 16,
>       javascript: 128
>   }
>   for (const [name, count] of Object.entries(books)) {
>       console.log(`${name.padEnd(16, '-')}|${count.toString().padStart(3, '0')}`)
>       /* 依次打印：
>       html------------|005
>       css-------------|016
>       javascript------|128 */
>   }
>   ```
>
>   * 在函数参数中添加尾逗号：允许在函数的参数最后添加一个 ```,```

## JavaScript 异步编程

> 概述：采用单线程模式工作，为避免复杂的线程同步问题，这里的单线程指的就是在 ```Js``` 执行环境中负责执行代码的线程只有一个（同时只能执行一个任务），如果同时有多个任务的话就需要排队，然后一个一个依次去完成，这种模式最大的优点就是：更安全、更简单，缺点同样也很明显：比如当我们遇到一个特别耗时的任务，那后面的这些任务都必须要去排队等待这个任务的结束，这就导致我们整个程序的执行会被拖延，出现假死的情况，为了解决耗时任务阻塞执行的这种问题，```JavaScript``` 将任务的执行模式分成了两种：
>
> * 同步模式（Synchronous）
> * 异步模式（Asynchronous）
>
> 内容概要：
>
> * 同步模式与异步模式：在表象上的差异以及它们各自存在的意义
> * 事件循环与消息队列：```JavaScript``` 的单线程是如何实现的异步模式
> * 异步编程的集中方式
> * ```Promise``` 异步方案、宏任务 / 微任务队列
> * ```Generator``` 异步方案、```Async``` / ```Await``` 语法糖 

#### 同步模式

> 同步模式指的就是代码中的任务依次执行，那么后一个任务就必须等待前一个任务结束才能够开始执行，程序的执行顺序跟我们的代码编写顺序是完全依次，也就是说这种方式会比较简单；在单线程的情况下，我们大多数任务都会以同步模式去执行，<font color=red>注意：</font>这里所说的同步，并不是指的同时执行，而是排队执行

#### 异步模式

> 不同与同步模式执行方式，异步模式的 ```API``` 是不会去等待这个任务的结束才开始去执行下一个任务，对于耗时操作，它都是开启过后就立即往后执行下一个任务，那耗时任务的后续逻辑一般会通过回调函数的方法去定义，在内部这个耗时任务完成过后就会自动执行我们这里传入的回调函数，异步模式对于 ```JavaScript``` 非常重要，因为如果没有这种模式的话，单线程的 ```JavaScript``` 语言就无法同时处理大量耗时任务；而对于开发者而言，单线程模式下面的异步最大的难点就是：代码中执行的顺序并不会像同步代码一样通俗易懂，因为它的执行顺序相对会比较跳跃

#### 回调函数

> 所有异步编程方案的根基
>
> 回调函数可以理解为一件你想要做的事情，你明确知道这件事情应该怎么做，怎么一步一步往下做，但是你并不知道这件事情所依赖的任务什么时候才能完成；所以说最好的办法就是：把你这件事情的步骤写到一个函数当中，交给任务的执行者，这个异步任务的执行者它是知道这个任务是什么时候结束的，那它就可以在结束过后去帮你执行你想要做的事情，这件想要做的事情就可以理解为：回调函数
>
> 有调用这定义，然后交给执行者去执行的函数就被称之为回调函数，将函数作为参数去传递

#### Promise

> <font color=orange>一种更优的异步编程统一发方案，用于解决回调地狱的问题</font>
>
> ```CommonJS``` 社区率先提出了 ```Promise``` 的规范，目的就是为异步编程去提供一种更合理、更强大的统一解决方案，后来在 ```ES2015``` 中被标准化，成为语言规范
>
> 实际上就是一个对象，用来表示一个异步任务最终结束过后它究竟是成功还是失败，就像是内部对外界做出了一个承诺，那一开始这个承诺是一个待定的状态（```Pending```），最终有可能成功（```Fulfilled```），也有可能失败（```Rejected```），在承诺状态最终明确了过后都会有相应的任务会被自动执行 ```Fulfilled => onFulfilled``` ```Rejected => onRejected```，而且这种承诺有一个很明显的特点：一旦明确了结果过后就不可能再发生改变了

###### 基本用法：

在代码层面 ```Promise``` 它实际上就是 ```ECMAScript2015``` 所提供的一个全局类型，可以用它来构造一个 ```Promise``` 实例，也就是创建一个新的承诺；这个类型的构造函数需要接收一个函数作为参数，这个函数就可以理解为一个兑现承诺的逻辑，在函数内部能够接收到两个参数，分别是（两者都为函数）： ```resolve``` => 将这个 ```Promise``` 的状态修改为 ```Fulfilled``` 也就是成功，一般将异步操作的结果通过这个 ```resolve``` 的参数传递出去；和 ```reject``` => 将这个 ```Promise``` 的状态修改为 ```Rejected``` 也就是失败，这里失败的参数一般传递的是一个错误的对象，用来表示这个承诺它为什么失败，也就是一个理由；在 ```Promise``` 接收的函数当中最后只能调用二者其一；```Promise``` 被创建完成之后，就可以使用这个实例的 ```then()``` 方法分别去指定 ```onFulfilled``` (第一个参数) 和 ```onRejected``` (第二个参数) 的回调函数

```javascript
const promise = new Promise(function (resolve, reject) {
    resolve(100)
})
promise.the(function (value) { // 成功后的回调
    console.log('resolved', value)
}, function (error) { // 失败后的回调
    console.log('rejected', error)
})

// Promise 方式的 Ajax
function ajax (url) {
  return new Promise(function (resolve, reject) {
      let xhr = new XMLHttpRequest()
      xhr.open('GET', url)
      xhr.responseType = 'json' // HTML5 中引入的新特性，在请求完成过后直接拿到一个 json 对象，而不是一个字符串
      xhr.onload = function () { // HTML5 当中提供的新事件
          if (this.status === 200) {
              resolve(this.response)
          } else {
              reject(new Error(this.statusText))
          }
      }
      xhr.send()
  })
}
ajax('./data.json').then(function (res) {
  console.log(res)
})
```

###### 链式调用：

> 最大程度的避免回调嵌套
>
> ```then``` 方法返回的是一个新的 ```Promise``` 对象（它与当前的 ```Promise``` 不是同一个对象，它并不是那种在内部返回 ```this``` 的方式去实现的链式调用），这是为了实现一个 ```Promise``` 的链条；如果添加多个链式调用 ```then``` 的方法，这里的每个 ```then``` 方法它实际上都是在为上一个 ```then``` 方法返回的 ```Promise``` 对象去添加状态明确过后的回调，也可以在 ```then``` 中函数中手动返回一个 ```Promise``` ，如果返回的值不是一个 ```Promise``` 而是一个普通的值，那么这个值将会作为当前的这个 ```then``` 方法返回的这个 ```Promise``` 中的值，那么在下一个 ```then``` 方法当中接收的这个回调参数的它实际上拿到的就是这样一个值
>
> * `Promise` 对象的 `then` 方法会返回一个全新的 `Promise` 对象
> * 后面的 `then` 方法就是上一个 `then` 返回的 `Promise` 注册回调
> * 前面 `then` 方法中回调函数的返回值会作为后面 `then` 方法回调的参数
> * 如果在回调中返回的是 `Promise` ，那后面 `then` 方法的回调会等待它的结果

###### 异常处理：

> `Promise` 的结果一旦失败，它就会调用我们在 `then` 方法中去传入的 `onRejected` 函数，如果在 `Promise` 的执行过程当中出现了异常或者是我们手动抛出了一个异常，那么 `onRejected` 回调也会被执行
>
> 使用 `Promise`  实例的 `catch` 方法去注册 `onRejected` 回调：
>
> ```javascript
> function ajax (url) {
>   return new Promise(function (resolve, reject) {
>       let xhr = new XMLHttpRequest()
>       xhr.open('GET', url)
>       xhr.responseType = 'json'
>       xhr.onload = function () {
>           if (this.status === 200) {
>               resolve(this.response)
>           } else {
>               reject(new Error(this.statusText))
>           }
>       }
>       xhr.send()
>   })
> }
> ajax('./data.json')
> .then(function (res) {
>   console.log(res)
> })
> .catch(function (error) {
>     console.log(error)
> })
> ```

###### 静态方法：

* `Promise.resolve()`：快速把一个值转换为一个 `Promise` 对象

  ```javascript
  Promise.resolve('foo').then(function (value) {
      console.log(value) // 打印：foo
  })
  // 它直接会返回一个状态为 Fulfilled 的 Promise 的对象，也就是成功的 Promise 对象，那么这里 'foo' 字符串将会作为这个 Promise 对象所返回的值，、
  
  // 如果接收到的是一个 Promise 对象，那么这个 Promise 对象会被原样返回
  let promise = ajax('./data.json')
  let promise1 = Promise.resolve(promise)
  console.log(promise === promise1) // 打印：true
  
  // 如果我们传入的是一个对象，而且这个对象也有一个跟 Promise 一样的 then 方法，也就是在这个方法中可以接收到 onFulfilled 和 onRejected 两个回调，然后去调用 onFulfilled 去传入一个值，那么这样一个对象也可以作为一个 Promise 被执行，在后面的 then 方法中也可以拿到这里所对应传入的值
  // 带有这种 then 方法的对象可以说是实现了一个叫做 thenable 的接口，也就是说它是个可以被 then 的对象
  ```

###### 并行执行：

* `Promise.all()`：可以将多个 `Promise` 合并为一个 `Promise` 统一去管理

* `Promise.all()` 中接收的是一个数组，数组当中的每一个元素都是一个 `Promise` 对象，我们可以把这些 `Promise` 都看作是一个一个异步任务，这个方法会返回一个全新的 `Promise` 对象，当内部的所有 `Promise` 都完成过后，我们所返回的这个全新的 `Promise` 才会完成，那此时这个 `Promise` 对象它拿到的结果就是一个数组，在这个数组里面包含着每个异步任务执行过后的结果

  > <font color=red>**注意：**</font>在这个任务的过程当中，只有当传入的 `Promise` 任务都成功结束了，这里的新的 `Promise` 才会成功结束，如果其中有任何一个任务失败了，那这个 `Promise` 就会以失败结束

  ```javascript
  function ajax (url) {
    return new Promise(function (resolve, reject) {
        let xhr = new XMLHttpRequest()
        xhr.open('GET', url)
        xhr.responseType = 'json'
        xhr.onload = function () {
            if (this.status === 200) {
                resolve(this.response)
            } else {
                reject(new Error(this.statusText))
            }
        }
        xhr.send()
    })
  }
  
  let promise = Promise.all([
      ajax('./data.json'),
      ajax('./data1.json')
  ])
  
  promise.then(function (values) {
      console.log(values) // 当所有 Promise 都成功结束后执行
  }).catch(function (error) {
      console.log(error) // 有一个  Promise 任务失败都将执行
  })
  ```

###### 执行时序 / 宏任务 vs. 微任务：

> 回调队列中的任务称之为 **宏任务**
>
> **宏任务** 执行过程中可以临时加上一些额外需要，这个时候对于这些临时额外的需求可以选择作为一个新的宏任务重新进入到回调队列当中去排队
>
> `setTimeout` 是以 **宏任务** 的形式进入到回调队列的末尾
>
> 也可以作为当前任务的 **微任务**，直接在当前任务结束过后立即执行，而不是到整个任务的末尾再重新排队
>
> `Promise` 的回调会作为微任务执行，所以它会在本轮调用结束的末尾去自动执行，**微任务** 的概念实际上是在后来才被引入到 `Js` 当中的，它的目的就是为了提高应用的响应能力
>
> 目前绝大多数异步调用都是作为 **宏任务** 执行的
>
> * 作为 **微任务** 执行
>   * `Promise`
>   * `MutationObserver`
>   * `Node` 中的 `process.nextTick`

#### Generator 异步方案

```javascript
function ajax (url) {
  return new Promise(function (resolve, reject) {
      let xhr = new XMLHttpRequest()
      xhr.open('GET', url)
      xhr.responseType = 'json'
      xhr.onload = function () {
          if (this.status === 200) {
              resolve(this.response)
          } else {
              reject(new Error(this.statusText))
          }
      }
      xhr.send()
  })
}

function * main () {
    try {
      const users = yield ajax('./data.json')
      console.log(users)
      const users1 = yield ajax('https://api.github.com/users')
      console.log(users1)
    } catch (e) {
        console.log(e)
    }
}
const g = main()
function handleResult (result) {
    if (result.done) return // 生成器结束
    result.value.then(data => {
        handleResult(g.next(data))
    }, error => {
        g.throw(error)
    })
}
handleResult(g.next())
```

#### Async / Await 语法糖

> 提供了扁平化的异步编程体验，语言层面标准的异步编程语法，使用起来更加方便
>
> `Await` 只能出现在 `Async` 定义的函数内

```javascript
function ajax (url) {
  return new Promise(function (resolve, reject) {
      let xhr = new XMLHttpRequest()
      xhr.open('GET', url)
      xhr.responseType = 'json'
      xhr.onload = function () {
          if (this.status === 200) {
              resolve(this.response)
          } else {
              reject(new Error(this.statusText))
          }
      }
      xhr.send()
  })
}

async function main () {
    try {
      const users = await ajax('./data.json')
      console.log(users)
      const users1 = await ajax('https://api.github.com/users')
      console.log(users1)
    } catch (e) {
        console.log(e)
    }
}
main()
```

