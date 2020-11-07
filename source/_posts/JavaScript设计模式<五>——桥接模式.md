---
title: JavaScript设计模式<五>——桥接模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<五>——桥接模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是桥接模式的学习。<!--more-->

- 什么是桥接模式？*将抽象部分与它的实现部分分离，使它们都可以独立地变化。它是一种对象结构型模式，又称为柄体(Handle and Body)模式或接口(Interface)模式*。—摘自[Graphic Design Patterns][1]

- 用通熟易懂的话来说，桥接模式可以将一个大类或一系列紧密相关的类拆分为抽象和实现两个独立的层次结构，从而能在开发时分别使用，并且在可以修改任意一个类层次结构时而不会影响到其他类层次结构。这种方法可以简化代码的维护工作， 并将修改已有代码的风险降到最低。

- 举个例子：假如你有一个游戏机`Device`类， 从它能扩展出两个子类： 索尼的`PS4`和微软的`XBoxOne`。你希望对这样的类层次结构进行扩展以使其包含版本， 所以你打算创建名为普通版`Nomal`和豪华版`Deluxe`的版本子类。但是，由于你已有两个子类，所以总共需要创建四个类才能覆盖所有组合，例如 PS4豪华版`PS4Deluxe`和XBoxOne普通版`XBoxOneNomal`。由此可见，如果我们要增加游戏记的种类或者增加版本的种类，彼时需要创建的类会越来越多（因为两者位于独立的维度）。

- 这个时候就需要用到桥接模式了，其通过将继承改为组合的方式来解决这个问题。具体来说，就是抽取其中一个维度并使之成为独立的类层次，这样就可以在初始类中用这个新层次的对象，从而使得一个类不必拥有所有的状态和行为。

- 接下来我们就用TypeScript和JavaScript一起来探究一下这个模式魅力，let's show code~

<center>![1.png](https://i.loli.net/2019/12/28/FLC9S65rxeXcWoM.jpg)</center>


一、分析过程
-------------------------

- 这一次我就拿之前举的游戏机例子来分析，我们先创建两个枚举类型`DeviceList`和`Version`，分别代表需要生产的游戏机与配套的版本：

```
enum DeviceList { 
    PS4 = 0,
    XBoxOne = 1
}
enum Version {
    Normal = 0,
    Deluxe = 1
}
```

- 然后我们需要知道，桥接模式中分为**抽象和实现两个独立的层次结构**，上面两个枚举类型是我们需要实现的类，我们命名为`Device`和`VersionInfo`，我们先完善`VersionInfo`类：

```
abstract class VersionInfo {
    abstract getVersionInfo():string ;
    /* other methods */
}

class NormalVersion extends VersionInfo {
    getVersionInfo():string {
        return Version[0];//Normal 普通版
    }
}

class DeluxeVersion extends VersionInfo {
    getVersionInfo():string {
        return Version[1];//Deluxe 豪华版
    }
}
```

- 根据我们`VersionInfo`类中提供的信息完善`Device`类：

```
abstract class Device {
    protected version:VersionInfo
    public getVersion():VersionInfo{
        return this.version;
    }
    public setVersion(version):void {
        this.version = version;
    }
    abstract showVersion():void
    /* other methods */
}

class PS4Producer extends Device {
    showVersion():void{
        console.log(`This ${DeviceList[0]}'s version: ${this.version.getVersionInfo()}`);
    }
}

class XBoxOneProducer extends Device {
    showVersion():void{
        console.log(`This ${DeviceList[1]}'s version: ${this.version.getVersionInfo()}`);
    }
}
```

- 到这一步，我们就算是将需要具体实现的类完成了，之后是抽象出组合它们的“桥”，这个“桥”是抽象部分，它通过在高层对实体类进行控制与工作委派，然后以此来产生组合后的实例。

```
class eProductBridge {
    private static _bridge: eProductBridge
    public static init():eProductBridge {
        if(!this._bridge) {
            this._bridge = new eProductBridge();
        } else throw new Error('Have defined!')
        return this._bridge;
    }
    public produce(device:DeviceList, version:Version):Device {
        let product: Device;
        console.log(device)
        console.log(version)
        switch(device) {
            case DeviceList.PS4:
                product = new PS4Producer();
                break;
            case DeviceList.XBoxOne:
                product = new XBoxOneProducer();
                break;
            default:
                throw new Error('No support Device')
        }
        switch(version) {
            case Version.Normal:
                product.setVersion(new NormalVersion());
                break;
            case Version.Deluxe:
                product.setVersion(new DeluxeVersion());
                break;
            default:
                throw new Error('No support version')
        }
        return product;
    }
}
```

- 为了确保抽象部分实例唯一，我们使用了单例模式来设计抽象部分。之后的`produce`方法使用了简单工厂模式，其根据传入的参数来生产实例。

- JavScript(ES6)版本与TS版也差不多，就是要实现enum类型麻烦一些。

```
let DeviceList = { 
    0:'PS4',
    'PS4': 0,
    1:'XBoxOne',
    'XBoxOne':1
}

class Device {
    version
    getVersion(){
        return this.version;
    }
    setVersion(version){
        this.version = version;
    }
    showVersion() {}
    /* other methods */
}

class PS4Producer extends Device {
    showVersion(){
        console.log(`This ${DeviceList[0]}'s version: ${this.version.getVersionInfo()}`);
    }
}

class XBoxOneProducer extends Device {
    showVersion() {
        console.log(`This ${DeviceList[1]}'s version: ${this.version.getVersionInfo()}`);
    }
}

let Version = {
    0:'Normal',
    1: 'Deluxe',
    'Normal':0,
    'Deluxe': 1
}

class VersionInfo {
    getVersionInfo(){} ;
    /* other methods */
}

class NormalVersion extends VersionInfo {
    getVersionInfo() {
        return Version[0];
    }
}

class DeluxeVersion extends VersionInfo {
    getVersionInfo() {
        return Version[1];
    }
}

class eProductBridge {
    static _bridge
    static init() {
        if(!this._bridge) {
            this._bridge = new eProductBridge();
        } else throw new Error('Have defined!')
        return this._bridge;
    }
    produce(device, version){
        let product;
        switch(device) {
            case DeviceList.PS4:
                product = new PS4Producer();
                break;
            case DeviceList.XBoxOne:
                product = new XBoxOneProducer();
                break;
            default:
                throw new Error('No support Device')
        }
        switch(version) {
            case Version.Normal:
                product.setVersion(new NormalVersion());
                break;
            case Version.Deluxe:
                product.setVersion(new DeluxeVersion());
                break;
            default:
                throw new Error('No support version')
        }
        return product;
    }
}
```

- 来测试一下结果吧：

```
let bridge = eProductBridge.init();
let otherBridge = eProductBridge.init();
// Uncaught Error: Have defined!

let ps4 = bridge.produce(DeviceList.PS4, Version.Deluxe)
ps4.showVersion();// This PS4's version: Deluxe

let xbox = bridge.produce(DeviceList.XBoxOne, Version.Normal)
xbox.showVersion();// This XBoxOne's version: Normal
```

<center>![2.png](https://i.loli.net/2019/12/28/qU4XySdT6pQYfJA.png)</center>
<center>![3.png](https://i.loli.net/2019/12/28/uCUEzxGKmsSV3Me.png))</center>


二、结语
-------------------------

- 桥接模式有许多的优点，而且这些优点笔者也在学习过程中能够体会得到：
    - 仅与高层抽象部分进行互动，不会接触到实体的详细信息。
    - 开闭原则：你可以新增抽象部分和实现部分，且它们之间不会相互影响。
    - 单一职责原则：抽象部分专注于处理高层逻辑，实现部分处理实体细节。

- 至于缺点由于本人才疏学浅未能看出这个模式有啥缺点，不过据说对高内聚的类使用该模式可能会让代码更加复杂。

- 下篇预告：策略模式~~🐦咕咕咕咕~~

- 参考资料
    - https://refactoringguru.cn/design-patterns/bridge
    - https://www.jianshu.com/p/ba239c1b767d
<br>

上传于2019-12-28


  [1]: https://design-patterns.readthedocs.io/zh_CN/latest/structural_patterns/bridge.html