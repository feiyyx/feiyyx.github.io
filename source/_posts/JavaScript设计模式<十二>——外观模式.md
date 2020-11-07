---
title: JavaScript设计模式<十二>——外观模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<十二>——外观模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是外观模式的学习。<!--more-->

- 什么是外观模式？根据[维基百科][1]：*外观模式（Facade pattern），是软件工程中常用的一种软件设计模式，它为子系统中的一组接口提供一个统一的高层接口，使得子系统更容易使用。*。

- 虽然都翻译为外观模式，但是根据各个网站和资料的讲解，个人更倾向于把Facade理解和翻译为为门面，也就是有些资料中说的门面模式，因为外观模式(Facade pattern)从描述上来说是将一个复杂的（拥有很多功能）系统或者很多子系统给整合起来，形成一个对外开放的接口站（含有很多整合后的接口），这样看来门面二字翻译得更为妥当，不过还是外观模式听起来好听hhhhh废话不多说，一起来写代码深入了解一哈。

<center>![1.jpeg](https://i.loli.net/2020/02/26/kMqxCztbu73Dm9P.jpg)</center>


一、分析过程
-------------------------

- 我们这次拿军队举个例子，军队里有不同的部队，像什么陆军`Army`、空军`Force`、海军`Navy`、~~炮兵~~火箭军`RocketForce`，还有负责后勤的后勤部队`Logistics`~~伙头军~~，那我们身为一个指挥他们的人，不可能事事亲为，所以我们整上一个指挥部`Command`来作为整合指挥调度部队的部门。选择陆军和后勤部队作为我们要调度的类。

```
interface Force {
    move():void;
    active():void;
}

class Army implements Force{
    move():void {
        console.log("Army Moving!")
    }
    active():void {
        console.log("Fire!")
    }
}

class Logistics implements Force{
    move():void {
        console.log("Logistics Moving!")
    }
    active():void {
        console.log("Supplying！")
    }
}

```

- 然后我们创建一个`Facade`外观类来整合两个部队一起移动，一起行动：

```
class Facade {
    protected army: Force;
    protected logistics: Force;
    constructor(army:Force = new Army(), logistics:Force = new Logistics()) {
        this.army = army;
        this.logistics = logistics;
    }
    operations():void {
        console.log("Army and Logistics move together.\n");
        this.army.move();
        this.logistics.move();
        console.log("Army and Logistics active.\n");
        this.army.active();
        this.logistics.active();
    }
}
```

- 之后我们试试效果：

```
var command = new Facade();
command.operations();
/*
Army and Logistics move together.
Army Moving!
Logistics Moving!
Army and Logistics active.
Fire!
Supplying！
*/
```

<center>![2.png](https://i.loli.net/2020/02/26/JVQfl8M1pnRXvaF.png)</center>

- 嗯，外观模式就是这么简单，JS版和TS版相差不多就不写了~~我又摸了~~

二、结语
-------------------------

- 这篇文章的内容是很简单，但是我还是写了挺久的，因为这个外观模式和和一些其他设计模式有很多相似的地方，想了很久总结了很久，发现都没有refactoringguru这里总结得到位，就干脆转载了它们网站的对于外观模式和其他设计模式的异同点分析，比我瞎口胡要好很多：
    - **外观模式**为现有对象定义了一个新接口， **适配器模式**则会试图运用已有的接口。**适配器**通常只封装一个对象，**外观**通常会作用于整个对象子系统上。
    - 外观与代理模式的相似之处在于它们都缓存了一个复杂实体并自行对其进行初始化。 **代理**与其服务对象遵循同一接口， 使得自己和服务对象可以互换， 在这一点上它**与外观不同**。

- 下篇预告：代理模式~~🐦咕咕咕咕~~

- 参考资料
    - https://refactoringguru.cn/design-patterns/facade
    - https://refactoringguru.cn/design-patterns/facade/typescript/example
    - https://www.jianshu.com/p/9d5d14f89dc8
<br>

上传于2020-02-26


  [1]: https://zh.wikipedia.org/zh-cn/%E5%A4%96%E8%A7%80%E6%A8%A1%E5%BC%8F