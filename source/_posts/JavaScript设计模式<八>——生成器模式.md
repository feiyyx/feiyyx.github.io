---
title: JavaScript设计模式<八>——生成器模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<八>——生成器模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是生成器模式的学习。<!--more-->

- 什么是生成器模式？*生成器模式又名：建造模式，是一种对象构建模式。它可以将复杂对象的建造过程抽象出来（抽象类别），使这个抽象过程的不同实现方法可以构造出不同表现（属性）的对象*。—摘自[维基百科][1]

- 生成器模式的建造过程有些类似工厂模式的生产过程，不过不同于工厂模式的生产模式（工厂模式直接以给好的流水线在内部进行生产最后产出产品，也就是不能定制属性），生成器模式生产产品的属性在客户端是可控可选可分步骤生成的。一起来show code康康这个模式的特点吧～

<center>![1.jpg](https://i.loli.net/2020/01/20/PIR5V6dZC9mbUNl.jpg)</center>


一、分析过程
-------------------------

- 生成器模式内部分为**生成器、具体生成器、产品、主管(Director)**四个模块，从字面意义上来看我们唯一不知道的是主管模块，主管模块又是什么呢？从各大网站获取的信息，总结一下就是**生成器内部的装配工厂**，可以作为预设设置而存在，因为生成器的生成步骤都是对客户端开放的，所以如果不需要默认设置，主管模块是可以取消掉的。

- 下面我们先定义一个生成器类`Computer`，为了~~摸鱼~~省事，我们仅选出了CPU、显卡和内存作为具体生成器中的生成步骤。先把产品和生成器部分完成：

```
/*-- 产品 --*/
interface Product{};
class Computer implements Product{
    public CPU;
    public RAM;
    public GAC;
    getInfo():void {
        console.log(`This computer's CPU is ${this.CPU}, RAM is ${this.RAM}, GAC is ${this.GAC}.`);
    }
}
/*-- 生成器和具体生成器 --*/
interface Builder {
    reset():void;
    setCPU(cpu:String):void;//CPU
    setRAM(ram:String):void;//内存
    setGAC(gac:String):void;//显卡
    getProduct():Product;
}

class ComputerBuilder implements Builder {
    private computer:Computer
    reset():void {
        this.computer = new Computer();
    }
    setCPU(cpu:String):void{
        this.computer.CPU = cpu;
    }
    setRAM(ram:String):void{
        this.computer.RAM = ram;
    }
    setGAC(gac:String):void{
        this.computer.GAC = gac;
    }
    getProduct():Product {
        let product = this.computer;
        this.reset();
        return product;
    }
}
```

- 然后是我们的主管Director模块，我们预设一台网友推荐的超级无敌土豪机器来作为主管模块的生成产品：

```
class Director {
    constructLUXCom(builder:Builder) {
        builder.reset();
        builder.setCPU("AMD Ryzen 9 3900X");
        builder.setRAM("64G");
        builder.setGAC("RTX2060TI");
    }
}
```

- JavaScript(ES6)版就把`interface`和`implements`都换成`class`和`extends`就行，以及把类型限制去掉～这里就懒得写了（逃）直接用TS编译后生成得JS：
```
/*TypeScript编译后得到的JS*/
var Prototype = /** @class */ (function () {
    function Prototype() {
    }
    Prototype.prototype.clone = function () {
        var target = new Prototype();
        target.obj = cloneProperties(this.obj);
        target.primitive = cloneProperties(this.primitive);
        return target;
    };
    return Prototype;
}());
var cloneProperties = function (prop) {
    var _newProp = null;
    if (typeof prop !== 'object' || prop === null || prop === undefined) {
        return prop;
    }
    else {
        switch (Object.prototype.toString.call(prop).substring(8).replace(']', "")) {
            case 'Array':
                _newProp = [];
                for (var key in prop) {
                    typeof prop[key] === 'object' ? _newProp[key] = cloneProperties(prop[key]) : _newProp[key] = prop[key];
                }
                break;
            case 'Object':
                _newProp = {};
                for (var key in prop) {
                    typeof prop[key] === 'object' ? _newProp[key] = cloneProperties(prop[key]) : _newProp[key] = prop[key];
                }
                break;
            case 'Date':
                _newProp = new Date();
                _newProp.setTime(prop.getTime());
                break;
            default:
                throw new Error("Weird Prop!");
        }
    }
    return _newProp;
};

```

- 测试一下结果：

```
var director = new Director();
var comBuilder = new ComputerBuilder();
director.constructLUXCom(comBuilder);
var product = comBuilder.getProduct();

product.getInfo();
// This computer's CPU is AMD Ryzen 9 3900X, RAM is 64G, GAC is RTX2060TI.
```

<center>![2.png](https://i.loli.net/2020/01/20/QDRbvxftLKo9aqp.png)</center>


二、结语
-------------------------

- 使用生成器模式的好处之前也说过一点，除了可分步实现，其还能以一种制作过程生成不同形式产品，不必像工程模式一样一件物品一个工厂（流水线）；并且在**执行制作步骤**时，产品未装配完前是不会对外部发布未完成的产品的，这样可以避免客户端代码获取到不完整结果对象的情况。

- 下篇预告：适配器模式~~🐦咕咕咕咕~~

- 参考资料
    - https://refactoringguru.cn/design-patterns/builder
<br>

上传于2020-01-20
修改于2020-06-09


  [1]: https://zh.wikipedia.org/zh-hans/%E5%8E%9F%E5%9E%8B%E6%A8%A1%E5%BC%8F