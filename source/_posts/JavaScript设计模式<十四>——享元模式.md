---
title: JavaScript设计模式<十四>——享元模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<十四>——享元模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是享元模式的学习。<!--more-->

- *享元模式是一种软件设计模式。它使用共享物件，用来尽可能减少内存使用量以及分享资讯给尽可能多的相似物件；它适合用于当大量物件只是重复因而导致无法令人接受的使用大量内存。通常物件中的部分状态是可以分享。常见做法是把它们放在外部数据结构，当需要使用时再将它们传递给享元*。—摘自[维基百科][1]

- 总结一下，简单来说，享元模式就是一种以时间换取空间的设计模式。为什么说是以时间换取空间，因为本来创建某一种对象的时候，当调用该对象创建方法的时候，是一条路走到底的。

- 而当使用享元模式的时候，因为剥离了对象的公有属性独立作为一个享元对象存在，此时再创建该对象的时候，首先会去享元工厂内检查是否有该对象所使用的属性，之后再根据返回的结果决定是复用还是创建新享元对象，最后再生成该对象，这样一通折腾时间就被拉长了，但是节省下了内存空间。

- 接下来是代码环节～

<center>![1.JPG](https://i.loli.net/2020/06/17/fzhtMY18CJpacvX.jpg)</center>


一、分析过程
-------------------------

- 享元模式内部分为**享元（对象外在状态）类、情景（对象内在状态）类、客户端和享元工厂**四个模块。

- **享元类**和**情景类**分别就是原始对象类拆分后的两个类，分别管理原始对象的不同方面的属性，总的来说，**享元类**就是负责存储对象不易改变或者干脆就是不会改变的属性，而**情景类**负责存储的则是对象里易于改变的属性，并且只读引用享元类中存储的实例后成为完整的原始对象。

- **享元工厂**会接收来自客户端的参数，然后负责根据这些参数在享元类中查找已存在属性或者添加未存在属性，最后返回该属性给客户端。

- **客户端**负责存储享元类和情景类的实例，并且会计算、调用享元对象，并且将这些参数传递给享元工厂获取属性，并且会将这些享元类作为配置数据传递给情景类，最后生成完整的原始对象。

- 来设想一个场景，你在做一款STG，然后要生成大量的士兵`Soldier`，但是做着做着发现当士兵生成地稍微多了一点就卡爆了，这个时候我们就可以使用我们的享元模式，把`Soldier`身上的属性分离一下：
  - 让`SoldierType`类存储士兵的兵种、血量、移动速度等属性；
  - 原本的`Soldier`作为一个类存储士兵的坐标和战斗指令即可，当有需要知道该士兵的其他属性，我们可以从`SolidierType`类中引用。下面是TS代码:

```
class SoldierType {
    public SoldierType: string;
    public SoldierSpeed: number;
    public SoldierHP: number;
    public TypeID: number;
}

class Soldier {
    public SoldierType: SoldierType;
    public name;
    public x: number;
    public y: number;
    constructor(x, y, name, SoldierType: SoldierType) {
        this.x = x;
        this.y = y;
        this.name = name;
        this.SoldierType = SoldierFactory.factory.getSoldierType(SoldierType);
    }
    public xMove() {
        this.x += this.SoldierType.SoldierSpeed;
        console.log(`Soldier ${this.name} move ${this.SoldierType.SoldierSpeed} along the X-axis`);
    }

    public yMove() {
        this.y += this.SoldierType.SoldierSpeed;
        console.log(`Soldier ${this.name} move ${this.SoldierType.SoldierSpeed} along the Y-axis`);
    }
}
```

- 然后就是建设我们的享元工厂，我们命名为`SoldierFactory`，我们想要获取或者新增SoldierType都在这里面进行实现，并且它还内部维护了一个SoldierType类型的数组以方便判断是否存在，在这个例子里还使用了单例模式限制工厂的唯一性。

```
class SoldierFactory {
    public static factory: SoldierFactory;
    public SoldierTypeCollection: SoldierType[];
    /*--设定新的兵种--*/
    public setSoldierType(type: SoldierType) {
        type.TypeID = this.SoldierTypeCollection.length+1;
        this.SoldierTypeCollection.push(type);
        console.log(type);
    }

    /*--根据ID获取列表内兵种--*/
    public getSoldierType(type: SoldierType) {
        for(let i in this.SoldierTypeCollection) {
            if(this.SoldierTypeCollection[i].TypeID === type.TypeID) {
                console.log('Existed!')
                return this.SoldierTypeCollection[i];
            } else {
                type.TypeID = this.SoldierTypeCollection.length+1;
                this.SoldierTypeCollection.push(type);
                return type;
            }
        }
    }

    /*--显示兵种列表--*/
    public showSoldierTypeList() {
        for(let i in this.SoldierTypeCollection) {
            console.log(`----------ID: ${this.SoldierTypeCollection[i].TypeID}-------------`)
            console.log(this.SoldierTypeCollection[i])
        }
    }

    /*--初始化工厂--*/
    public static initFactory() {
        if(SoldierFactory.factory === undefined) {
            SoldierFactory.factory = new SoldierFactory();
            SoldierFactory.factory.SoldierTypeCollection = [];
        } else {
            console.log('Factory has already created!');
        }
    }
}
```

- 来看一下代码的整体效果：

```
SoldierFactory.initFactory()
SoldierFactory.factory.setSoldierType({'SoldierType': 'Infantry', 'SoldierSpeed': 2, 'SoldierHP': 100, 'TypeID': null})
// {SoldierType: "Infantry", SoldierSpeed: 2, SoldierHP: 100, TypeID: 1}

SoldierFactory.factory.setSoldierType({'SoldierType': 'Cavalry', 'SoldierSpeed': 5, 'SoldierHP': 200, 'TypeID': null})
// {SoldierType: "Cavalry", SoldierSpeed: 5, SoldierHP: 200, TypeID: 2}
SoldierFactory.factory.showSoldierTypeList();
// 这个有点多，看图就好。。。

let soldier_01 = new Soldier(1, 1, 'Sam', {'SoldierType': 'Infantry', 'SoldierSpeed': 2, 'SoldierHP': 100, 'TypeID': 1});
soldier_01.xMove();
// Soldier Sam move 2 along the X-axis

let soldier_02 = new Soldier(2, 2, 'Alan', {'SoldierType': 'Sniper', 'SoldierSpeed': 1, 'SoldierHP': 50, 'TypeID': null});
soldier_02.yMove();
// Soldier Alan move 1 along the Y-axis

SoldierFactory.factory.showSoldierTypeList();
// 也是有点多，看下面的图吧

```

<center>![2.png](https://i.loli.net/2020/06/18/VWKrIa9HgikxwqX.png)</center>


二、结语
-------------------------

- 享元模式个人认为在游戏里使用得较为多一些，在前端方面除了H5游戏，应该就是花里胡哨的炫技背景之类的了吧。

- 最后强调一点，享元模式里的享元是不可变的，只能新增不能修改。笔者个人猜测可能是因为修改后代价太大吧，牵一发而动全身的感觉。

- P.S. 终于有时间好好回头来学习一下了Orz，顺带还花了点时间把自己以前写的重新看了一遍，感觉理解更深了那么一丢丢XD。

- 下篇预告：组合模式~~🐦咕咕咕咕~~

- 参考资料
    - https://refactoringguru.cn/design-patterns/flyweight
<br>

上传于2020-06-18


  [1]: https://zh.wikipedia.org/zh-cn/%E4%BA%AB%E5%85%83%E6%A8%A1%E5%BC%8F