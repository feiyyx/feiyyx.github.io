---
title: JavaScript设计模式<三>——单例模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<三>——单例模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是单例模式的学习。<!--more-->

- 什么是单例模式？*单例模式，也叫单子模式，是一种常用的软件设计模式。在应用这个模式时，**单例对象的类必须保证只有一个实例存在**。许多时候整个系统只需要拥有一个的全局对象，这样有利于我们协调系统整体的行为*。——摘自维基百科

- 看完上面那段话基本上也就明白什么是单例模式了，在前端中的单例模式最常见的有浏览器里的window对象、Vue框架中**main.js**初始化时候的`new Vue()`等，他们都是相应的类只有一个实例存在于全局环境中。为了能够了解一点这些单例对象的运行原理，这次就来学习一下单例模式。

<center>![1.png](https://i.loli.net/2019/12/21/dKVtTU6SpEN43kr.jpg)
</center>


一、分析过程
-------------------------

- 由于已经学完了TypeScript，所以为了多练习，以后都用它来写学习代码了，~~所以标题名存实亡了~~ JavaScript和TypeScript在形式上还是挺类似的，通过TypeScript编译后生成的JS来学习最原生的js也是一个不错选择，笔者也会在使用TypeScript写完一遍后再使用JS(ES6)在写一遍。

- 单例模式首先需要一个对象来生成实例，所以我们设定一个`Singleton`类，并为其设置`name`和`discription`两个私有属性，以及为了确认对象的唯一性而引入的静态私有属性`singleInstance`。

```
class Singleton {
    private name: string
    private discription: string
    private static singleInstance: Singleton
    private constructor(name:string = null) {
        this.name = name;
        this.discription = "This is a singleton";
    }
    /* methods */
}
```

- 然后我们设置一个public方法`setInstance`来生成我们的单例对象的实例，并设置相应判断来确定生产的实例唯一：

```
class Singleton {
    ...
    public static setInstance(name:string):Singleton {
        if(!Singleton.singleInstance) {
            Singleton.singleInstance = new Singleton(name);
        } else {
            console.log("Have been created!");
        }
        console.log(Singleton.singleInstance);
        return Singleton.singleInstance;
    }
}
```

- 最后加入一个public方法`log`来查看该单例的属性：

```
class Singleton {
    ...
    public log(): void {
        console.log(this.discription);
        console.log(`which name is ${this.name}`);
        console.log(Singleton.singleInstance);
    }
}
```

- 单例模式JavaScript(ES6)版：

```
class Singleton {
    static singleInstance;
    constructor(name) {
        this.name = name;
        this.discription = "This is a singleton";
    }
   static setInstance (name) {
        if (!Singleton.singleInstance) {
            Singleton.singleInstance = new Singleton(name);
        } else {
            console.log("Have been created!");
        }
        console.log(Singleton.singleInstance);
        return Singleton.singleInstance;
    }
   log() {
        console.log(this.discription);
        console.log(`which name is ${this.name}`);
        console.log(Singleton.singleInstance);
   }
}
```

- 让我们将它们放入浏览器console看看效果：

```
var single = Singleton.setInstance("single");
// Singleton {name: "single", discription: "This is a singleton"}

var double = Singleton.setInstance("double");
// Have been created!
// Singleton {name: "single", discription: "This is a singleton"}

single.log()
// This is a singleton
// which name is single
// Singleton {name: "single", discription: "This is a singleton"}
```

<center>![2.png](https://i.loli.net/2019/12/21/wOmxYd6znFfvBs9.png)</center>

- 可以看到输出结果符合预期。

二、结语
-------------------------

- 简单地实现了一下前端中的单例模式，在其中也借鉴了一篇文章，也回头看了看TS的教程，果然看了几遍和实际上手操作还是不太一样。单例模式虽然不难，但是这个概念是需要我们牢牢记住的，未来参与开发框架的时候也许会有要用到的地方。

- 至于为啥这一期没更新工厂模式……emmmm虽然它不难，但是实际上工厂模式远比我想象得要稍微复杂一些，等我查一些资料自己写一写再拿出来献丑hhh

- 下期预告：工厂模式~~在看了在看了咕咕咕咕🐦~~

- 参考资料
    - https://jkchao.github.io/typescript-book-chinese/tips/singletonPatern.html
<br>
上传于2019-12-21

