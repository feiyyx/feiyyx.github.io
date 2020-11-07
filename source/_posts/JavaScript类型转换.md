---
title: JavaScript类型转换
tags: 
      - JavaScript
---

JavaScript类型转换
=================================

一、前言
--------------------------
 - JavaScript的类型转换对我来说一直以来都是个神奇的问题，于是开个坑来做一下实验。<!--more-->
 
二、正文
--------------------

### JavaScript的基础类型
 
- JavaScript的基础数据类型总共有六个：**字符串（String）、数字(Number)、布尔(Boolean)、对空（Null）、未定义（Undefined）**，还有ES6中新加入的**Symbol。**

- 这几个基础的数据类型加上**Object**就构成了我们写的经常JavaScript整体。以下的讨论中暂时不带Symbol类型玩，具体为什么看到中间就知道了。

### 类型转换
 
- JavaScript的数据类型变化是很好玩（神奇）的，让我们一起来看看下面的代码：

```
let number = 123;
let str1 = '456';
let str2 = 'test'
let tr = true;
let fa = false;
let nu = null;
let un = undefined;

console.log(number+str1+str2+tr+fa+nu+un) // output:"123456testtruefalsenullundefined"
```

- 由此可以记得如果字符串与其他几种数据类型相加，**他们都会先转换成字符串然后拼接在一起**，这点和python还蛮像的，让我们再看看下面的代码：

```
let number = 123;
let tr = true;
let fa = false;

console.log(number + tr);// output: 124
console.log(number + fa);// output: 123
```
- 这里大家也是能看得出来在JavaScript中，数字类型与布尔类型在一起进行加减的时候，布尔类型会先转换为数字类型进行计算（即Number(Boolean_Val))，true转换为数字1，false转换为数字0，到目前为止也都还挺正常的。让我们接着再往下走：

```
let number = 123;
let nu = null;
let un = undefined;

console.log(number + nu); // output: 123
console.log(number + un); // output: NaN

```

- 到了这里就开始有点emmmm……NaN（~~Not a Number~~）是什么鬼啦！

![smjb.jpg-12.3kB][1]

- 通过测试和观察，我们所能知道的是，数字类型与这两个值进行相加计算的时候，也是会调用Number()先处理这两个类型的数，然后因为Number(null)=0和Number(undefined)=NaN,然后NaN与任何数字相加都是NaN，所以答案就是这样了。顺带一提：

> console(NaN + 'test') // output: 'NaNtest'，是的string类型就是这么强大hhhhh

- 然后我们再来看点别的：

```
let tr = true;
let nu = null;
let un = undefined;

console.log(tr + nu); // output: 1
console.log(tr + un); // output: NaN
console.log(nu + un); // output: NaN
```

- 从上面的代码和输出我们可以看出：JavaScript将这些类型都转换成了数字类型后再进行相加，Emmmmm真的是太神奇了。

- 由此我们可以的出结论，在基础类型的相加中：
    - **当相加的变量中含有String类型的时候，优先调用toString()方法将所有其他变量均转换为String类型后再进行拼接，返回的结果是个*字符串***。

    - **当相加的变量中不含有String类型的时候，调用Number()方法将所有其他变量转换为Number类型后再进行相加操作，返回的结果是个*Number* 或者*NaN***。
    
- 顺带一提，Symbol类型比较特殊，任何类型与它相加都会返回：

> Uncaught TypeError: Cannot convert a Symbol value to a xxx(与其相加的类型如string等）

### 基于JS类型转换的的小技巧

- 我在网上搜罗了一些可以在开发过程中常用的小技巧来提升我们的开发效率，可以很轻松的实现类型转换，使得代码可以得到优化。

#### 1、转换为String

- 由于前面说的所有类型加上String类型的数据都会转换为String类型，所以我们可以利用这点，用上一点小技巧：

```
let x = 123;
let y = x + ''; // y = '123'
console.log(typeof y);// output: "string"
```

- 上面所示这个方法可以快速将某一类型数据快速转换为string类型。

#### 2、转换为Number

- 这里同样也有相似的技巧将String转换为Number：

```
let x = '123'
let y = +x // y = 123
let z = ~~x // z = 123 

console.log(typeof y) // output: "number"
console.log(typeof z) // output: "number"
```

- 至于这是为什么呢……我在网上找到的解释是：**'+'号运算符作为一元运算符时，表达式将进行Number()操作。**再深层次的原因我觉得应该就要去看JS的底层实现了，总之我们使用的时候先这么记着就行。

- 第二个的话解释是这样的：**一个波浪号~，被称为“按位不运算符”，等价于 - n - 1。所以~15 = -16。**所以使用两次后就变成了 **- (-n-1) - 1 = n**

#### 3、转换为Boolean

- 虽然在JavaScript中，除了其值为 **0、-0、null、false、NaN、undefined、或者空字符串（""）**，则生成的 Boolean 对象的值为 false，任何其他的值，包括值为 "false" 的字符串和任何对象，都会创建一个值为 true 的 Boolean 对象。 ——摘自MDN

- 不过我们还可以调用两次'!'运算符来将其他类型的数据转换为对应的布尔型：

```
let x = !!'test'

console.log(x) // output: true
console.log(typeof x) // output: "boolean"
```

- 其实这个方法我们平时还用的挺频繁的，例如反转判断逻辑：

> if (!array.includes(val)) { ... }

三、结尾
----------------------------------------  

- 以上介绍的类型转换是建立在各个数据类型默认的valueOf()与toString()方法上。至于对象中自定义valueOf与toString以及ES6中新引入的Symbol.toPrimitive就不在本文讨论范围内了，感兴趣的朋友可以移步[这篇文章][2]了解一下。

- 参考借鉴文章：
    - [11个教程中不常被提及的JavaScript小技巧][3]
    - [\[译\]JavaScript中,{}+{}等于多少?][4]
    - [JavaScript 加号运算符详解][5]
    - [MDN 《JavaScript 文档 > Boolean》][6]

上传于 2019-4-24


  [1]: http://static.zybuluo.com/feiyyx/645u0rs1d2xp96x9nkv8vqmv/smjb.jpg
  [2]: https://godbmw.com/passages/2019-03-26-javascript-first/
  [3]: https://segmentfault.com/a/1190000018897633#articleHeader12
  [4]: https://www.cnblogs.com/ziyunfei/archive/2012/09/15/2685885.html
  [5]: https://www.cnblogs.com/polk6/p/js-adv-addopr.html
  [6]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Boolean