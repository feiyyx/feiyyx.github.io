---
title: JavaScript设计模式<十七>——命令模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<十七>——命令模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是命令模式的学习。<!--more-->

- 什么是命令模式？*在面向对象设计的范畴中，命令模式是一种设计模式，它尝试以物件来代表实际行动。命令物件可以把行动及其参数封装起来，于是这些行动可以被： 重复多次 取消 取消后又再重做 这些都是现代大型应用程式所必须的功能，即「复做」及「重复」。*。—摘自[维基百科][1]

- 不过一般来说，只要应用到了把一系列行为封装为具体命令，并且触发者(invoker)可以通过这些命令将自己想要做的事情发送给接收者(Reciver)，最后接收者根据传来的命令完成自己的工作；那么这一个流程就可以说是命令模式。

- 乍一看其实和我们熟知的什么订阅发布模式还有其他几个模式还蛮像的，其实他们还是有区别的，文章最后再梳理他们之间的区别，先介绍一下命令模式，let's show code。


<center>![1.jpg](https://i.loli.net/2020/11/29/JHiNftby12EYn6a.jpg)</center>


一、分析过程
-------------------------

- 命令模式实质上是解耦了触发者和接收者，因为触发者不关心最终的接收者是谁，他只关心最终接收者收到后达成的功能是否是他想要的，所以我们可以将它们进行解耦，以命令(Command)作为两者之间的桥梁。

- 命令接收从触发者拿到的指令，通过分析后将其分配给具体命令，具体命令再将这个指令传递给接收者的特定功能，最后接收者完成工作。下面就以现实生活中的顾客到餐厅点餐来做个例子。

- 通过上面的方法分析，我们可以发现顾客相当于是触发者`Invoker`，主厨相当于接收者`Receiver`，而服务员给顾客点菜，再将顾客的菜单拿给主厨这一整个过程就相当于是命令`Command`；因为顾客并不关心是谁给他做的菜，他们只需要那道菜最后被呈上来而已。

- 第一步，先声明对应的类，因为已经知道对应的场景是什么了，所以我们可以将与该命令有联系的接收者写死在`Order`类中（~~正式开发的时候好孩子不要学哦~~）

```
interface Invoker {};
interface Receiver {};
abstract class Command {
    protected cheif: Chief;
    
    public abstract setCheif(receiver: Receiver);

    public abstract execute(meal: String);
}
```

- 之后我们再细化这几个方法：

```
class OrderCommand extends Command {
    public setCheif(receiver: Chief) {
        this.cheif = receiver;
    }

    public execute(meal: String) {
        this.cheif.cook(meal);
    }
}

class Chief implements Receiver {
    public name: String;
    constructor(name: String) {
        this.name = name;
    }

    public cook(meal: String) {
        console.log(this.name, 'cooks the meal:', meal);
    }
}

class Custom implements Invoker {
    public waiter: OrderCommand;
    public callWaiter(waiter: OrderCommand) {
        this.waiter = waiter;
    }

    public order(meal: String) {
        console.log('I order the', meal);
        this.waiter.execute(meal);
    }
}
```

- 最后测试我们的代码：

```
const cheif = new Chief('The Cheif');
const waiter = new OrderCommand();
waiter.setCheif(cheif);

const custom = new Custom();

custom.callWaiter(waiter);

custom.order('steak'); 
// "I order the steak"
// "The Cheif cooks the meal: steak"
```

<center>![2.png](https://i.loli.net/2020/11/29/EtVuhbZXPALocyY.png)</center>


二、结语
-------------------------

- 命令模式其实也就是这么简单，而且这个模式运用十分广泛，像是我们经常使用的Typora和VScode都采用了这种模式（~~应该说只要是个编辑器都使用了这种模式~~）。

- 不过很容易把它和其他几个设计模式搞混，所幸refactoringguru中帮我们总结了它与其他几个类似的模式的不同之处：<u>责任链模式、 命令模式、 中介者模式和观察者模式用于处理请求发送者和接收者之间的不同连接方式</u>

	- `责任链`按照**顺序**将请求动态传递给一系列的潜在接收者， **直至其中一名**接收者对请求进行处理。

	- `命令`在发送者和请求者之间建立**单向连接**。

	- `中介者`清除了发送者和请求者之间的直接连接， **强制**它们通过一个中介对象进行间接沟通。

	- `观察者`允许接收者**动态地订阅或取消**接收请求。

- 下篇预告：迭代器模式~~🐦咕咕咕咕~~

- 参考资料
    - 【refactoringguru】[责任链模式][2]
<br>

上传于2020-11-29


[1]: https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwi80Ky34KbtAhVFPHAKHeeqAC0QmhMwEXoECA4QAg&url=https%3A%2F%2Fzh.wikipedia.org%2Fzh-tw%2F%25E5%2591%25BD%25E4%25BB%25A4%25E6%25A8%25A1%25E5%25BC%258F&usg=AOvVaw1s7nrWli0WDn1LsdwRoGQO
[2]: https://refactoringguru.cn/design-patterns/command