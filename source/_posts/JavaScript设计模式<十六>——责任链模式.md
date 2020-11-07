---
title: JavaScript设计模式<十六>——责任链模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<十六>——责任链模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是责任链模式的学习。<!--more-->

- 什么是责任链模式？*责任链模式在面向对象程序设计里是一种软件设计模式，它包含了一些命令对象和一系列的处理对象。每一个处理对象决定它能处理哪些命令对象，它也知道如何将它不能处理的命令对象传递给该链中的下一个处理对象。该模式还描述了往该处理链的末尾添加新的处理对象的方法*。—摘自[维基百科][1]

- 就如同百科所说，使用了这一模式的程序在收到请求后，每个处理者均可对请求进行处理，或将其传递给链上的下一个处理者。这样的特性其实很像我现在在的手机总装流水线上的工作，在拿到从流水线上游获得的物料后，检查物料是否可以通过自己这项工序的检查或者能否继续组装，若是出现问题则通知对应的负责人进行处理，没有出现问题则交给下一个工序的人处理。

- 哦挖槽，这两者特性还真的蛮像的，是不是冥冥之中自有天意让暂时化身为工人的我来写这篇责任链模式XD。废话少说，下面就show code吧。

<center>![1.jpg](https://i.loli.net/2020/09/12/unN4As3RtavZkEW.jpg)</center>


一、分析过程
-------------------------

- 这个设计模式其实真不算是很复杂的模式，我个人认为是属于比较简单就能够理解的设计模式；不过想要最大程度发挥它的用处就得在开始使用它之前做好业务逻辑的需求分析，最好画个图将这条链上的各个处理者的功能明确，以确保这个链表是最简洁、最少冗余的。

- 首先我们先确定处理者所拥有的功能，最简单的就是设置这条链的下一个处理者和它所对应的处理方法，我们命名为`setNext()`和`handle()`。

```
interface Handler {
    setNext(handler: Handler):Handler;
    handle(request: string):string;
}
```

- 之后我们使用一个抽象类来实现处理者的基础功能和确定内部变量：

```
abstract class AbstractHandler implements Handler {
    private nextHandler: Handler
    public setNext(handler: Handler):Handler {
        this.nextHandler = handler;
        return this.nextHandler;
    }

    public handle(request: string): string {
        if(this.nextHandler) {
            return this.nextHandler.handle(request);
        }
        return null;
    }
}
```

- 这里的私有变量nextHandle直接指向下一个处理者，相当于一个单向链表(单向链表比较简单XD)，然后完善了两个方法的功能。之后我们就通过这个抽象类拓展出我们所需要的实体类：

```
class DiamondVIP extends AbstractHandler {
    public handle(request: string):string {
        if(request === '钻石会员') {
            return `您的身份是【${request}】`;
        }
        return super.handle(request);
    }
}

class GoldVIP extends AbstractHandler {
    public handle(request: string):string {
        if(request === '黄金会员') {
            return `您的身份是【${request}】`;
        }
        return super.handle(request);
    }
}

class SilverVIP extends AbstractHandler {
    public handle(request: string):string {
        if(request === '白银会员') {
            return `您的身份是【${request}】`;
        }
        return super.handle(request);
    }
}

class BronzeVIP extends AbstractHandler {
    public handle(request: string):string {
        if(request === '青铜会员') {
            return `您的身份是【${request}】`;
        }
        return super.handle(request);
    }
}
```

- 都是很简单的类，来用简单的例子来测试一下结果：

```JavaScript
const diamondVIP = new DiamondVIP();
const goldVIP = new GoldVIP();
const silverVIP = new SilverVIP();
const bronzeVIP = new BronzeVIP();

bronzeVIP.setNext(silverVIP).setNext(goldVIP).setNext(diamondVIP);

// 这里我直接使用了refactoringguru的测试方法XD，小小偷个懒
function clientCode(handler: Handler) {
    const identities = ['钻石会员', '黄金会员', '青铜会员'];

    for (const identity of identities) {
        console.log(`进行责任链测试 ${identity}`);

        const result = handler.handle(identity);
        if (result) {
            console.log(`  ${result}`);
        } else {
            console.log('未检测到您的身份。');
        }
    }
}

console.log('Chain: 青铜会员 > 黄金会员 > 钻石会员\n');
clientCode(bronzeVIP);
// 进行责任链测试 钻石会员
// 您的身份是【钻石会员】
// 进行责任链测试 黄金会员
// 您的身份是【黄金会员】
// 进行责任链测试 青铜会员
// 您的身份是【青铜会员】
console.log('');

console.log('Subchain: 黄金会员 > 钻石会员\n');
clientCode(goldVIP);
// 进行责任链测试 钻石会员
// 您的身份是【钻石会员】
// 进行责任链测试 黄金会员
// 您的身份是【黄金会员】
// 进行责任链测试 青铜会员
// 未检测到您的身份
```

<center>
![2.png](https://i.loli.net/2020/09/12/R864H1VXtGOlhq7.png)</center>


二、结语
-------------------------

- 若是一个业务逻辑能够按照传入的值按顺序执行与处理，那么这样的逻辑其实就很适合使用责任链模式，我能够想到的有OA系统中请假审批的功能，以及注册时确认身份(通过手机验证码等)等等。

- 还有两个我没能想到的应用场景，refactoringguru里也给出了：
    - 如果所需处理者及其顺序必须在运行时进行改变，可以使用责任链模式。
    - 当程序需要使用不同方式处理不同种类请求，而且请求类型和顺序预先未知时，可以使用责任链模式。

- 使用责任链模式的最大的**好处**就在于其符合开闭原则，我们可以向里面新增链条而不修改原有的代码；但是其**坏处**就在于由于是单链条，所以很可能会出现部分请求直到链尾都没法被处理，这也是我们需要去思考的。

- 下篇预告：命令模式~~🐦咕咕咕咕~~

- 参考资料
    - 【refactoringguru】[责任链模式][2]
    - 【refactoringguru】[TypeScript 责任链模式讲解和代码示例][3]
<br>

上传于2020-09-13


  [1]: https://zh.wikipedia.org/zh-hans/%E5%8E%9F%E5%9E%8B%E6%A8%A1%E5%BC%8F
  [2]: https://refactoringguru.cn/design-patterns/chain-of-responsibility
  [3]: https://refactoringguru.cn/design-patterns/chain-of-responsibility/typescript/example