---
title: JavaScript设计模式<一>——发布-订阅模式
tags: 
      - JavaScript
      - 设计模式
---

JavaScript设计模式<一>——发布-订阅模式
=================================

〇、前言
--------------

- 为了让自己能够有更多的底气阅读源码，所以特此开一个新坑来学习JavaScript中的设计模式。<!--more-->

- 为什么选择订阅-发布模式来作为本系列的开端呢，因为本辣鸡下定决心准备在最近一段时间开展钻研Vue源码的行动，所以打算先从发布-订阅模式和观察者模式这两个设计模式下手开始研究，这样为后面的源码研究夯实基础。

- 而且这两个模式感觉适用的范围很广，以后开发的时候说不定也能用上。

<center>![1.JPG](https://i.loli.net/2019/12/16/CIVd5OW1qFYlrnE.jpg)</center>


一、介绍
-------------------------

- 《JavaScript设计模式》里说到发布-订阅模式是观察对象及其行为和状态之间的关系，在JS中我们可以通过这个模式对程序中的某个对象进行观察，并且在其发生改变的时候能够获得通知。

- 网上虽然有些资料说发布-订阅模式和观察者模式是一个东西，但是其实他们俩并不能完全说是一种东西，关于这点可以看[这篇文章][1]讲的比较好。知乎上关于这个的问题回答也很优秀，感兴趣的同学可以自己去搜索一下。下面我开始讲一下我自己的思路。

二、分析过程
-------------------------

- 发布-订阅模式别看它的名字叫发布订阅，但是其实它包含了三个部分——发布者、订阅者以及一个用来接收发布消息，通知订阅者的调度中心。下面这种图就能很直观的看出三者的关系：

<center>![2.png](https://i.loli.net/2019/12/16/4cYmBWXksE9pHrU.png)</center>

- 在我思考写这个设计模式的时候，我选择先将这个调度中心的功能给实现了，然后将订阅者的功能也实现，最后再实现发布者的功能。首先来看调度中心，我们需要的一个对象来容纳订阅者，然后实现接受发布信息、通知订阅者的功能：

```
class Dep {
    constructor() {
        this.subs = {};
    }
    subscribe(suber) {
        /* subscribe */
    }
    publish(pub) {
        /* pulish */
    }
}
```

- 然后我们实现一个订阅者的类，就假定我们要接受一个`getMsg`的变量，然后订阅者也有自己的`name`，并且为了展示效果，我们再实现一个`log`方法来查看订阅者是否收到了`getMsg`，最后我们通过在方法内调用我们所要进行订阅的调度中心，然后将自己本身这个实例传过去：:

```
class Sub {
    constructor(name) {
        this.name = name;
        this.getMsg = "";
    }
    log() {
        console.log(`Subscribe ${this.getMsg}`);
    }
    subscribe(dep) {
        dep.subscribe(this)
    }
}
```

- 完成订阅者的类之后，我们再回过头来将调度中心的`subscribe`方法完善，首先判断是否在列表中存在该对象，若不存在则以该对象的`name`属性命名，然后放入列表中。

```
class Dep {
    ...
    subscribe(suber) {
        if(this.subs.hasOwnProperty(suber.name))
            console.log("Already susbscribe")
        else this.subs[suber.name] = suber;
    }
    ...
}
```

- 然后我们实现发布者的类，发布者需要发布一个名为`message`的信息给调度中心，这里我思考的也是在方法内调用我们所要进行发布的调度中心，然后将自己本身传过去：

```
class Pub {
    constructor(message) {
        this.message = message;
    }
    publish(dep) {
        dep.publish(this);
    }
}
```

- 顺着前面思考，在发布者的`publish`方法里调用调度中心的`publish`方法且传入的是某一个发布者的实例的本身。那么我们要做的就是**遍历列表，查看该调度中心是否有订阅者实例的订阅信息**；若发现有，则将该发布者实例发布的信息逐一传入订阅了该调度中心的订阅者实例内。可能这一段会有点绕，看代码就懂了：

```
class Dep {
    ...
    publish(pub) {
        console.log("Publish Msg to Dep!");
        for(var item in this.subs) {
            this.subs[item].getMsg = pub.message;
        }
    }
}
```

- 到这一步，这个最基础的发布-订阅模式就做好了。接下来我们看一下效果：

```
let x = new Sub("x");
let pub = new Pub("Hello World");
let dep = new Dep(); 

x.subscribe(dep);
x.subscribe(dep); // "Already susbscribe"
pub.publish(dep); // "Publish Msg to Dep!"
x.log(); //"Subscribe Hello World"
```

<center>![3.png](https://i.loli.net/2019/12/16/xBP4ACeFWm6ny52.png)</center>

- 是我们想要的效果～

三、结语
-------------------------

- 由于这次只是实现了最基础的的发布-订阅模式，所以难免会有很多不完善的地方，例如订阅者应该可以对自己接收的消息设置条件，然后调度中心再根据条件进行filter等，不过作为体验一下这种设计模式应该足够了。

- 我所了解到用到了这个模式的就有redis的channel分发、Vue2的双向绑定等，所以使用还是十分广泛的，学好了肯定杠杠的XD

- 下期预告：观察者模式~~咕咕咕咕🐦~~

<br>
上传于2019-12-16


  [1]: https://juejin.im/post/5a14e9edf265da4312808d86