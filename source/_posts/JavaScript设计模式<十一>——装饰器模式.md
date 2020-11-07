---
title: JavaScript设计模式<十一>——装饰器模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<十一>——装饰器模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是装饰器模式的学习。<!--more-->

- 什么是装饰器模式？根据[维基百科][1]：*是面向对象编程领域中，一种动态地往一个类中添加新的行为的设计模式。就功能而言，装饰器模式相比生成子类更为灵活，这样可以给某个对象而不是整个类添加一些功能*。

- 需要注意的点：装饰器模式不是TS里的装饰器，装饰器模式是面向整个系统的一个设计模式，而语言中的装饰器是用来给其他类/方法以一行代码的形式提供额外添加的功能。还是实地使用一下代码来看看装饰器模式长啥样吧，here we go～

<center>![1.jpg](https://i.loli.net/2020/02/17/zdDVS7cqOaCfRJh.jpg)</center>


一、分析过程
-------------------------

- 我们这次拿台式电脑来举例子，因为现在大家都喜欢DIY自己的主机/键盘/屏幕什么的，我们先设置一个`Computer`类的interface，然后以此衍生出我们的个人PC以及它的主机、键盘和屏幕的功能类：

```
interface Computer {
    chassis():void;
    screen():void;
    keyboard():void;
}

class PersonalComputer implements Computer {
    chassis():string{
        return "开机"
    }
    screen():string{
        return "显示图形界面"
    }
    keyboard():string{
        return "输入"
    }
}

```

- 然后我们根据我们的`PersonalComputer`类建立一个它的装饰器类`ComputerDecoractor`，并且方法都掉用原本类的方法：

```
class ComputerDecoractor implements Computer {
    protected component: Computer;
    constructor(component: Computer) {
        this.component = component;
    }
    chassis():string{
        return this.component.chassis();
    }
    screen():string{
        return this.component.screen();
    }
    keyboard():string{
        return this.component.keyboard();
    }
}
```

- 之后我们就可以继承这个类来真正拓展我们的`PersonalComputer`类了：

```
class DIYDecoractor extends ComputerDecoractor {
    chassis():string{
        return `\nLED发光 \n${super.chassis()}`
    }
    screen():string{
        return `\n安装摄像头 \n${super.screen()}`;
    }
    keyboard():string{
        return `\n更换键帽 \n${super.keyboard()}`;
    }
}
```

- ES6版本与TS版本类似，删去返回类型和interface即可，测试一下我们之前写的代码：

```
let pc = new PersonalComputer();
let diy = new DIYDecoractor(pc);
diy.chassis();
/***
"
LED发光 
开机
"
***/

diy.screen();
/***
"
安装摄像头 
显示图形界面
"
***/

diy.keyboard();
/***
"
更换键帽 
输入
"
***/
```

<center>![2.png](https://i.loli.net/2020/02/17/AQm5P8XbF6u2Iqp.png)</center>


二、结语
-------------------------

- 从代码来看，装饰器模式仅仅是在原本的系统上增加了一层“壳”，原本的系统还是可以使用的，并且这层“壳”也仅仅是增加了想要添加的功能而并没有修改原本的系统，这样我们就不用创建一个新的子类就可以“修改”系统获得我们所需要的功能。

- 与适配器模式的区别：适配器模式可以对已有对象的接口进行修改（接口名可能发生改变），且原有系统可能存在兼容问题；而装饰模式则能在不改变对象接口的前提下强化对象功能（不修改原有功能与方法名），且原系统功能完好且也可在当前环境下使用

- 下篇预告：外观模式~~🐦咕咕咕咕~~

- 参考资料
    - https://refactoringguru.cn/design-patterns/decorator
    - https://github.com/forthealllight/blog/issues/33
<br>

上传于2020-02-17


  [1]: https://zh.wikipedia.org/zh-cn/%E4%BF%AE%E9%A5%B0%E6%A8%A1%E5%BC%8F