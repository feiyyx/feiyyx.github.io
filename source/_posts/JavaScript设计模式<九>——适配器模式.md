---
title: JavaScript设计模式<九>——适配器模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<九>——适配器模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是适配器模式的学习。<!--more-->

- 什么是适配器模式？从字面上来看，就是给系统装配一个能够适合它配置的仪器。根据[维基百科][1]：*在设计模式中，适配器模式有时候也称包装样式或者包装。将一个类的接口转接成用户所期待的。一个适配使得因接口不兼容而不能在一起工作的类能在一起工作，做法是将类自己的接口包裹在一个已存在的类中*。

- 所以我们可以很清楚的得知适配器模式的用处就在于使得不同类之间接口可以兼容使用，看来这个模式以后肯定会经常用上www，let's show code.

<center>![1.jpg](https://i.loli.net/2020/02/04/3GmKa7XACbPlgn6.jpg)</center>


一、分析过程
-------------------------

### 对象适配器

- 适配器模式是十分简单的，虽然其分为类适配器和对象适配器两类，我们先来一起看看最简单的对象适配器：*实现时使用了构成原则：适配器实现了其中一个对象的接口，并对另一个对象进行封装。*（节选自refactoringguru）基于它的特点，以及我从网上找到的例子，我们可以设定一个场景：一个老系统里用的是老版本的ES，所以我们的函数获取的是三个参数；但是我们现在的客户端传入的数据改为了json，我们就可以写一个对象适配器来适配，代码很简单：

```
/* An old system */
class systemA {
    public static instance = new systemA()

    public printTime(year: string, month: string, day: string):void { 
        console.log(`The time is ${year}-${month}-${day}`);
    }
}
/* adapter */
/* 对象适配器 */
interface adapter {
    adapterPrintTime: (time: {year: string, month: string, day: string}) => any;
}

class systemA_Adapter implements adapter {
    public adapterPrintTime(time: {year: string, month: string, day: string}): any {
        systemA.instance.printTime(time.year, time.month, time.day);
    }
}
```

- ES6的JS和这个也差不多，就不写了（~~摸了摸了~~），让我们看看效果

```
/*client */
let data = {
    day: '02',
    month: '02',
    year : '2020'
}
let useSystemA = new systemA_Adapter();
useSystemA.adapterPrintTime(data);
//The time is 2020-02-02
```

<center>
![2.png](https://i.loli.net/2020/02/04/MVvYyBdsG7KbJe2.png)</center>

### 类适配器

- 然后是类适配器，*这一实现使用了继承机制：适配器同时继承两个对象的接口。*，这个设计模式可以直接继承接口，也适合兼容没有单例模式存在的系统，所以我们只需要稍稍修改一下之前的代码：

```
/* An old system */
class systemA {
    public printTime(year: string, month: string, day: string):void { 
        console.log(`The time is ${year}-${month}-${day}`);
    }
}

/* adapter */
interface adapter {
    adapterPrintTime: (time: {year: string, month: string, day: string}) => any;
}
/* 类适配器 */
class classAdapter extends systemA implements adapter {
    public adapterPrintTime(time: {year: string, month: string, day: string}): any {
        this.printTime(time.year, time.month, time.day);
    }
}
```

- 测试一下结果：

```
let data = {
    day: '02',
    month: '02',
    year : '2020'
}

let useSystemA = new classAdapter();
useSystemA.adapterPrintTime(data);
//The time is 2020-02-02
```

<center>![3.png](https://i.loli.net/2020/02/04/E6wpZj3sPmdUKFX.png)</center>


二、结语
-------------------------

- 适配器模式乍看之下和桥接模式有那么点相似，但是他们适用的地方不一样：适配器模式更倾向在已有的程序/系统中使用，使得不兼容的类可以一起工作；而桥接模式则是在设计系统的时候直接从种类出发，将大类划分为可以组合的抽象的小类，之后通过抽象的小类进行组合实现完整的不同的系统。

- 下篇预告：状态模式~~🐦咕咕咕咕~~

- 参考资料
    - https://refactoringguru.cn/design-patterns/adapter
    - https://www.jianshu.com/p/e0bda9224d9e
    - https://juejin.im/post/5a2d16325188252da0535d73
<br>

上传于2020-02-04


  [1]: https://zh.wikipedia.org/zh-cn/%E9%80%82%E9%85%8D%E5%99%A8%E6%A8%A1%E5%BC%8F