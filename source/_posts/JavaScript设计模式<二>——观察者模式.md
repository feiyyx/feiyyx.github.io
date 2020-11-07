---
title: JavaScript设计模式<二>——观察者模式
tags: 
      - JavaScript
      - 设计模式
---

JavaScript设计模式<二>——观察者模式
=================================

〇、前言
--------------

- 今天带来的是观察者模式。<!--more-->

- 为什么要把观察者模式和发布-订阅模式分开讲呢，因为我觉得虽然两者很像，但是其实还是区别很大的，例如耦合度的关系：观察者模式的耦合度就比发布-订阅模式要高；又比如两个模式下关注的点也不同，观察者模式需要知道彼此的存在，而发布-订阅模式则是通过调度中心来联系发布/订阅者。~~其实是为了水文章。~~

- 不过两者还是可以合在一起看看的，所以这篇和上一篇对比着来看风味更佳哦。Let's show code~。

<center>![1.png](https://i.loli.net/2019/12/18/4onu37rbqpDksAz.jpg)</center>


一、介绍
-------------------------

- 还是回到《JavaScript设计模式》里说的那段话：发布-订阅模式是观察对象及其行为和状态之间的关系，在JS中我们可以通过这个模式对程序中的某个对象进行观察，并且在其发生改变的时候能够获得通知。观察者模式也是同样的道理，它也能起到这个作用。

- 在这个设计模式下，消息的接收方和发送方分为观察者和被观察者，如下图左边所示。这个时候，细心的朋友就会发现其实被观察者一方是将发布者与调度中心耦合在了一起，它独立完成消息推送与管理订阅者这两个功能；而观察者其实就是订阅者。

<center>![2.png](https://i.loli.net/2019/12/16/4cYmBWXksE9pHrU.png)</center>


二、分析过程
-------------------------

- 正如上一部分提到的那样，观察者等同于订阅者，被观察者等同于发布者+调度中心。所以我选择先从被观察者下手，最后再写观察者的类。

- 在被观察者类中，我们需要一个`subscribers`属性来存储订阅者，并且再设置一个`message`属性来存储发布的消息，由于耦合了发布者与调度中心，所以这个被观察者类上需要有他们的功能，所以我们加入发布消息的方法`publish`与添加订阅的方法`subscribe`。

```
class Subject {
    constructor() {
        this.subscribers = {};
        this.message = "";
    }
    publish(val) {
        /* pulish */
    }
    subscribe(obs) {
        /* subscribe */
    }
}
```

- 然后我们再在观察者类中设置相应的类，和之前发布-订阅模式中的`Sub`类的功能和实现方法基本一样，只不过要使用`delete`方法来删除订阅对象中被选中的对象：

```
class Obeserver {
    constructor(name) {
        this.name = name;
        this.getMsg = "";
    }
    subscribe(sub) {
        sub.subscribe(this);
    }
    log() {
        console.log(`getMsg ${this.getMsg}`);
    }
}
```

- 根据需求完善被观察者：

```
class Subject {
    ...
    publish(val) {
        this.message = val;
        console.log("Publish message to Subject!");
        for(var item in this.subscribers) {
            this.subscribers[item].getMsg = this.message;
        }
        return val;
    }
    subscribe(obs) {
        if(this.subscribers.hasOwnProperty(obs.name))
            console.log("Already susbscribe")
        else this.subscribers[obs.name] = obs;
    }
}
```

- 到这里最基础的观察者模式也就完成了，不过这次我们想加入一个退订的功能，我们给每个类都加上一个`off`方法来达成退订的功能，实现方法与订阅方法类似。

```
class Obeserver {
    ...
    off(sub) {
        sub.off(this);
    }
}

class Subject {
    ...
    off(obs) {
        console.log("Off the Observer");
        if(this.subscribers.hasOwnProperty(obs.name)) {
            let temp = obs.name;
            delete this.subscribers[temp]
        }
        else console.log("No such a Observer!")
    }
}
```

- 让我们看一下效果：

```
let x = new Obeserver("x");
let sub = new Subject();

x.subscribe(sub);
x.subscribe(sub); // "Already susbscribe"
x.off(sub);
x.off(sub); // "No such a Observer!"
x.subscribe(sub);
x.subscribe(sub); // "Already susbscribe"

sub.publish("Hello Observer!") // "Publish message to Subject!"
x.log() // "getMsg Hello Observer!"
```

<center>![3.png](https://i.loli.net/2019/12/19/GrYs9ywbFUZSB6d.png)</center>

- 可以看到输出结果符合预期。

三、结语
-------------------------

- 这次也只是简单介绍一下JS中的观察者模式，正如我一直说的那样，这些代码还有很多不足，只能作为这个设计模式的入门来进行参考，如有不对，恳请斧正。如果我能抛自己的砖，引读者的玉，那便是再好不过了。

- 下期预告：单例模式和工厂模式~~咕咕咕咕🐦~~

<br>
上传于2019-12-19

