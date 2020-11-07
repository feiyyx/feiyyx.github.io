---
title: JavaScript设计模式<四>——工厂模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<四>——工厂模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是工厂模式的学习。<!--more-->

- 什么是工厂模式？*工厂方法模式（英语：Factory method pattern）是一种实现了“工厂”概念的面向对象设计模式。就像其他创建型模式一样，它也是处理在不指定对象具体类型的情况下创建对象的问题。工厂方法模式的实质是“定义一个创建对象的接口，但让实现这个接口的类来决定实例化哪个类。工厂方法让类的实例化推迟到子类中进行*。

- *创建一个对象常常需要复杂的过程，所以不适合包含在一个复合对象中。创建对象可能会导致大量的重复代码，可能会需要复合对象访问不到的信息，也可能提供不了足够级别的抽象，还可能并不是复合对象概念的一部分。工厂方法模式通过定义一个单独的创建对象的方法来解决这些问题。由子类实现这个方法来创建具体类型的对象。*——摘自维基百科

- 工厂模式不仅仅只有我们平时见到的简单工厂模式（使用switch或其他简单方法创建产品），还有工厂方法模式和抽象工厂模式。下面我就来一一解释这几种模式和他们之间的区别。

<center>![1.jpg](https://i.loli.net/2019/12/25/ukxATCe19FyQGmc.jpg)</center>


一、分析过程
-------------------------

### 简单工厂模式

- 首先我们先从简单工厂模式入手，简单工厂模式也有一些可以说道说道的：

- *简单工厂模式描述了一个类，它拥有一个包含大量条件语句的构建方法，可根据方法的参数来选择对何种产品进行初始化并将其返回。*

- *人们通常会将简单工厂与普通的工厂或其它创建型设计模式混淆。在绝大多数情况下，简单工厂是引入工厂方法或抽象工厂模式时的一个中间步骤。*——摘自[《工厂模式比较》][1]

- 概括来说，简单工厂模式就是将同类型的对象整合在一起，然后可以通过这个工厂来决定创建某一个新的对象实例（产品），我们先设定一个`Laptop`类来充当我们的产品，然后围绕这个`Laptop`类来衍生产品：

```
interface Laptop {
    system(): void;
}

class MacPro implements Laptop {
    system():void {
        console.log("Mac Pro's system => MacOS Catalina");
    }
}

class Surface implements Laptop {
    system():void {
        console.log("Surface Pro's system => Windows 10");
    }
}
```

- 实现起来也相当简单，JS的实现方法与TS方法类似：

```
class laptopFactory {
    static produce(laptop):any {
        switch(laptop) {
            case "SurfacePro": return new SurfacePro();
            case "MacPro": return new MacPro();
            default: throw Error('produce nothing! ');
        }
    }
}
```

- 实现效果如下：

```
var mac = laptopFactory.produce("MacPro");
var surface = laptopFactory.produce("SurfacePro");
var test = laptopFactory.produce("test");// Uncaught Error: produce nothing! 


mac.system();// Mac Pro's system => MacOS Catalina
surface.system();// Surface Pro's system => Windows 10

```

<center>![2.1.png](https://i.loli.net/2019/12/25/9Rm5nXELKPt2OwZ.png)</center>
<center>![2.2.png](https://i.loli.net/2019/12/25/x2HTRFuYq8tcrQe.png)</center>


### 工厂方法模式

- 接下来是工厂方法模式，这种工厂模式是将工厂抽象化，然后使用抽象化后的工厂作为主类，然后让子工厂来生产属于自己的产品。

```
abstract class laptopFactory {
    protected name:string
    protected system:string
    constructor(name:string, system:string) {
        this.name = name;
        this.system = system;
    }
}

class macFactory extends laptopFactory {
    constructor(name:string, system:string) {
        super(name, system);
    }
    systemLog():void {
        console.log(`${this.name}'s system => ${this.system}`);
    }
}
class surfaceFactory extends laptopFactory {
    constructor(name:string, system:string) {
        super(name, system);
    }
    showSystemLog():void {
        console.log(`${this.name}'s system => ${this.system}`);
    }
}
let mac = new macFactory("MacPro", "MacOS Catalina");
let surface = new surfaceFactory("SurfacePro", "Windows 10");

mac.systemLog();
surface.showSystemLog();
```

- 细心的朋友可能看到一半就发现两个类展示系统名称的方法并不一样，这就是前面所说的“子工厂生产属于自己的产品”，由此可见这个工厂方法模式并不是简单工厂模式的升级版，而是**更加灵活**，其可以使得每种产品都封装属于自己的工厂方法来生产产品。当然**缺点**也是显而易见的，就是会增加代码复杂程度。

- ES6版的JavaScript写法：

```
class laptopFactory {
    constructor(name, system) {
        this.name = name;
        this.system = system;
    }
}
class macFactory extends laptopFactory {
    constructor(name, system) {
        super(name, system);
    }
    systemLog(){
        console.log(`${this.name}'s system => ${this.system}`);
    }
}
class surfaceFactory extends laptopFactory {
    constructor(name, system) {
        super(name, system);
    }
    showSystemLog() {
        console.log(`${this.name}'s system => ${this.system}`);
    }
}
```

- 可以看见与TS版差别很小，让我们将它们放入浏览器console看看效果：

```
let mac = new macFactory("MacPro", "MacOS Catalina");
let surface = new surfaceFactory("SurfacePro", "Windows 10");

mac.systemLog(); // MacPro's system => MacOS Catalina
surface.showSystemLog();// SurfacePro's system => Windows 10
```

<center>![3.png](https://i.loli.net/2019/12/25/jnQBUKZEJFc1bVi.png)</center>

- 我们可以看到不同工厂生产出来的产品（实例）有**不同**的方法\[1\]，这是简单工厂模式所不能办到的。这一模式的好处就是想要加入其他产品类的时候只需要再增加工厂及其产品的类即可即可，不过代价就是增加一个产品可能要增加多个类，可以说有利有弊。


\[1\] 虽然功能一样，但是为了以示工厂方法模式中工厂生产的类可以有不同方法，所以特地使用不同命名区别——笔者注 

### 抽象工厂模式

- 然后是抽象工厂模式，这一设计模式适用于生产一系列产品，举个例子——虽然我知道这种情况并不可能总是发生——当你生产MacPro的时候总要配套生产一个妙控鼠标（MagicMouse），生产SurfacePro的时候总要搭配一个ArcTouch鼠标。这个时候我们就可以使用抽象工厂模式。

- 我们首先将`Laptop`和`Mouse`两个产品类的设置好。

```
interface Laptop {
    system(): void;
}
class MacPro implements Laptop {
    system():void {
        console.log("Mac Pro's system => MacOS Catalina");
    }
}
class SurfacePro implements Laptop {
    system():void {
        console.log("Surface Pro's system => Windows 10");
    }
}

interface Mouse {
    name:string
}
class MagicMouse implements Mouse {
    name:string = "Magic Mouse"
}
class ArcTouch implements Mouse {
    name:string = "Arc Touch"
}
```
- 然后再将工厂抽象化，并生产这两种产品：

```
abstract class Factory {
    protected name:string
    protected mouse:Mouse
    protected laptop: Laptop
    constructor(name:string) {
        this.name = name;
    }
    abstract createLaptop(): Laptop;
    abstract createMouse(): Mouse;
}

class macFactory extends Factory {
    constructor(name:string) {
        super(name);
    }
    createLaptop(): MacPro{
        this.laptop = new MacPro()
        return this.laptop;
    }
    createMouse(): MagicMouse {
        this.mouse = new MagicMouse();
        return this.mouse;
    }
    log():void {
        this.laptop.system();
        console.log(`${this.name}'s mouse => ${this.mouse.name}`);
    }
}
class surfaceFactory extends Factory {
    constructor(name:string) {
        super(name);
    }
    createLaptop(): SurfacePro{
        this.laptop = new SurfacePro()
        return this.laptop;
    }
    createMouse(): ArcTouch {
        this.mouse = new ArcTouch();
        return this.mouse;
    }
    showLog():void {
        this.laptop.system();
        console.log(`${this.name}'s mouse => ${this.mouse.name}`);
    }
}
```

- *JS(ES6)版：*

```
class Laptop {
    system() {
        return "system"
    }
}
class MacPro extends Laptop {
    system(){
        return "Mac Pro's " + super.system() + " => MacOS Catalina"
    }
}
class SurfacePro extends Laptop {
    system() {
        return "Surface Pro's " + super.system() + " => Windows 10"
    }
}

class Mouse {
    name = "name"
}
class MagicMouse extends Mouse {
    name = "Magic Mouse"
}
class ArcTouch extends Mouse {
    name = "Arc Touch"
}

class Factory {
    static mouse
    static laptop
    constructor(name) {
        this.name = name;
    }
    createLaptop() {
        this.laptop = new Laptop();
        return this.laptop;
    }
    createMouse() {
        this.mouse = new Mouse();
        return this.mouse;
    }
}
class macFactory extends Factory {
    constructor(name) {
        super(name);
    }
    createLaptop(){
        this.laptop = new MacPro()
        return this.laptop;
    }
    createMouse() {
        this.mouse = new MagicMouse();
        return this.mouse;
    }
    log() {
        console.log(this.laptop.system());
        console.log(`${this.name}'s mouse => ${this.mouse.name}`);
    }
}
class surfaceFactory extends Factory {
    constructor(name) {
        super(name);
    }
    createLaptop(){
        this.laptop = new SurfacePro()
        return this.laptop;
    }
    createMouse(){
        this.mouse = new ArcTouch();
        return this.mouse;
    }
    showLog(){
        console.log(this.laptop.system());
        console.log(`${this.name}'s mouse => ${this.mouse.name}`);
    }
}
```

- 结果：

```
var mac = new macFactory("MacPro");
mac.createLaptop();
mac.createMouse();
var surface = new surfaceFactory("SurfacePro");
surface.createLaptop();
surface.createMouse();

mac.log();
// Mac Pro's system => MacOS Catalina
// MacPro's mouse => Magic Mouse

surface.showLog();
// Surface Pro's system => Windows 10
// SurfacePro's mouse => Arc Touch
```

<center>![4.png](https://i.loli.net/2019/12/25/63M7jokTqpGtVzH.png)</center>

- 抽象工厂方法使得使用者只需要关注抽象工厂与抽象产品即可，不需要明白具体实现细节。

二、结语
-------------------------

- 工厂模式带来的便利就在于外部**不用**也**不允许**关心工厂内部是如何实现的，只需要调用工厂方法生成实例（产品）即可，这符合了开放封闭原则。

- 我们所使用的各大框架或多或少都有类似的功能使用了该模式，比较典型的就有JQuery的`$()`，React的`React.createElement()`方法以及Vue的创建组件方法`Vue.component()`。至于项目中的实战可以看[这篇文章][2]，我认为作者写得很有心。

- 下篇预告：桥接模式~~🐦咕咕咕咕~~

- 参考资料
    - https://github.com/brookshi/Blog/blob/master/design%20pattern/TypeScript%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B9%8B%E5%B7%A5%E5%8E%82.md
    - https://refactoringguru.cn/design-patterns/factory-comparison
<br>
    - https://juejin.im/post/5b69c699e51d45348a301ef4
上传于2019-12-25


  [1]: https://refactoringguru.cn/design-patterns/factory-comparison
  [2]: https://juejin.im/post/5b69c699e51d45348a301ef4#heading-4