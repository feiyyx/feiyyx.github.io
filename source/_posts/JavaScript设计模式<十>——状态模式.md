---
title: JavaScript设计模式<十>——状态模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<十>——状态模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是状态模式的学习。<!--more-->

- 什么是状态模式？根据[维基百科][1]：*。此模式接近于有限状态机的概念。状态模式可以解释为策略模式，它能够通过调用模式界面中定义的方法来切换策略。在计算机编程中使用状态模式来根据对象的内部状态封装相同对象的变化行为*。

- 简单来说，一个策略代表系统所处某一个状态所要做的行动，最后的形式有点类似状态机的组成：

<center>![ex.jpg](https://i.loli.net/2020/02/12/dGJQH3cRE4Nfg2k.jpg)</center>

- 多说无益，我们还是来康康代码怎么写8。

<center>![0.jpg](https://i.loli.net/2020/02/12/AXMG7SPgiHDYlnq.jpg)</center>


一、分析过程
-------------------------

- 我们可以假设一个最简单音乐播放器，它只有播放和停止两个功能，我们把他们设置为两个状态`PlayState`和`StopState`，然后有两个切换状态的按钮`clickStop`和`clickPlay`，�由于播放和停止时，再点击这两个按键不会造成状态的改变，所以我们直接log输出相关文字：

```
interface State {
    handle(): void;
    clickStop(context: Context):void ;
    clickPlay(context: Context):void ;
}

class StopState implements State {
    private context: Context
    handle() {
        console.log("Music Stop Now!");
    }
    clickStop(context: Context) {
        console.log("Music Already Stoped.Doing Nothing.");
    }
    clickPlay(context: Context) {
        //TODO
    }
}

class PlayState implements State {
    private context: Context
    handle() {
        console.log("Music Play Now!");
    }
    clickStop(context: Context) {
        //TODO
    }
    clickPlay(context: Context) {
        console.log("Music Already Played.Doing Nothing.");
    }
}

```

- 然后是状态机的上下文，它可以通过状态接口与状态对象交互，且会提供一个设置器用于传递新的状态对象，所以根据它的性质我们将两个按钮的接口放入其中，并且创造一个私有变量存储当前状态：

```
class Context {
    private state: State;

    constructor(state:State = new StopState()) {
        this.state = state
        this.state.handle();
    }

    set State(state:State) {
        this.state = state;
    }

    get State():State {
        return this.state;
    }

    public clickStop():void {
        this.state.clickStop(this);
    }

    public clickPlay():void {
        this.state.clickPlay(this)
    }
}
```

- 然后根据这个上下文再在状态里完成切换状态的接口：

```
class StopState implements State {
    ...
    clickPlay(context: Context) {
        console.log("Music Ready to Play!");
        context.State = new PlayState();
        context.State.handle();
    }
}

class PlayState implements State {
    ...
    clickStop(context: Context) {
        console.log("Music Ready to Stop!");
        context.State = new StopState();
        context.State.handle();
    }
    ...
}
```

- ES6版本与TS版本类似，故不重写一遍了，测试一下结果：

```
let audio = new Context();
// Music Stop Now!

audio.clickPlay();
// Music Ready to Play!
// Music Play Now!

audio.clickPlay();
// Music Already Played.Doing Nothing.

audio.clickStop();
// Music Ready to Stop!
// Music Stop Now!

audio.clickStop();
// Music Already Stoped.Doing Nothing.
```

<center>![1.png](https://i.loli.net/2020/02/12/kGu1dYvI6iUqVT7.png)</center>


二、结语
-------------------------

- 状态模式更适合将一个复杂的拥有许多状态判断逻辑的系统分解成一个有着许多简单状态的状态机。我个人认为Vuex和Redux都使用或者说借鉴了这个模式，它们都是对框架内全局变量的状态进行统一管理。

- 下篇预告：装饰器模式~~🐦咕咕咕咕~~

- 参考资料
    - https://refactoringguru.cn/design-patterns/state
    - https://github.com/semlinker/typescript-design-patterns/tree/master/src/state
<br>

上传于2020-02-12


  [1]: https://en.wikipedia.org/wiki/State_pattern