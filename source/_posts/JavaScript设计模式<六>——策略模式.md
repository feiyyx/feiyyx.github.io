---
title: JavaScript设计模式<六>——策略模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<六>——策略模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是策略模式的学习。<!--more-->

- 什么是策略模式？*指对象有某种行为，但是在不同的场景中，该行为有不同的实现算法（这一行为称为一族算法）。策略模式不仅定义了这一族算法（业务规则），并且封装了一族中的每个算法，使得这族的每个算法可互换代替（interchangeable）使用*。—摘自[维基百科][1]

- 简单的来说就是将同一类型的算法从主程序中抽离出来，并且通过封装成类生成实例来给主程序调用（前提是该算法与主程序耦合程度低且拥有一定复杂度）。具体的结构我觉得refactoringguru中[这一节][2]的图不错，借来用一哈，相信大家都能康懂：

<center>![1.png](https://i.loli.net/2020/01/13/sMrkS7wjq8zc4WE.png)</center>

- 看着一大堆概念可能会觉得有点难，但是其实使用起来也不算很难，接下来我们就用TypeScript和JavaScript一起来试一试，let's show code~

<center>![2.jpg](https://i.loli.net/2020/01/13/xfvEUdt5yKhQNjX.jpg)</center>


一、分析过程
-------------------------

- 这一次拿人民币兑美元和日元来举例子，首先我们先实现`Strategy`类的部分，因为是汇率，一切从简的话就选个大概的数字来充当`exchange`方法中的兑换比率：

```
interface Strategy {
    exchange(val:number):void
}
class RMBtoJPY implements Strategy {
    exchange(val:number) {
        console.log(`${val} RMB = ${val*15.86} JPY`);
    }
}
class RMBtoUSD {
    exchange(val:number) {
        console.log(`${val} RMB = ${val*0.14} USD`);
    }
}
```

- 之后是上下文`Context`，既然是汇率，那么我们就要存储一个RMB数额`value`和当前使用的策略`strategy`，然后使用`setStrategy`来设置当前使用的策略，接收参数为`Strategy`类：

```
class Context {
    private strategy:Strategy
    private value: number
    constructor(value:number) {
        this.value = value;
    }
    public setStrategy(stra:Strategy) {
        this.strategy = stra;
    }
}
```

- 最后一步就是执行当前策略，我们设置为方法`execute`。

```
class Context {
    ...
    public execute():void {
        this.strategy.exchange(this.value);
    }
}
```

- 然后一个简单的策略模式例子就完成叻，就是这么简单。JS版与TS版相差不大就不写了~~（懒死我）~~


- 来测试一下结果吧：

```
let context = new Context(1000);
context.setStrategy(new RMBtoJPY());
context.execute();

context.setStrategy(new RMBtoUSD());
context.execute();
```

<center>![3.png](https://i.loli.net/2020/01/13/16J4aBuSH8mArPD.png)</center>


二、结语
-------------------------

- 为什么选择策略模式？这样做的好处就是减少了成堆的条件判断语句，且因为是封装成类，所以增加新功能时可以通过继承与组合来实现代码的复用与拓展，且符合开闭原则。~~妈妈再也不用担心我写一堆if-else/switch了~~

- 下篇预告：原型模式~~🐦咕咕咕咕~~

- 参考资料
    - https://refactoringguru.cn/design-patterns/strategy
    - https://www.jianshu.com/p/4412fb24ea03
<br>

上传于2020-01-13


  [1]: https://zh.wikipedia.org/zh-cn/%E7%AD%96%E7%95%A5%E6%A8%A1%E5%BC%8F
  [2]: https://refactoringguru.cn/design-patterns/strategy