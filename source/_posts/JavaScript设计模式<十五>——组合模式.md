---
title: JavaScript设计模式<十五>——组合模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<十五>——组合模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是组合模式的学习。<!--more-->

- *在软件工程中，组合模式是一种分区设计模式。组合模式描述了一组对象，这些对象被视为同一类型对象的单个实例。组合的目的是将对象“组合”成树形结构以表示部分-整体层次结构。实施组合模式可以使客户端统一对待各个对象和构图。*。—摘自[维基百科][1]

- 组合模式的关键在于“组合”两字，为了使得这些组件能够组合在一起，我们需要给这些或复杂，或简单的组件都设置一个通用的接口，然后通过这个通用的接口来逐级调用组件，层层递进，最后将结果返回给客户端。这样说得还有些抽象，用代码来说明比较好。

<center>![1.jpg](https://i.loli.net/2020/06/24/l7Pu3njkQmeqBvC.jpg)</center>


一、分析过程
-------------------------

- 组合模式结构分为**叶节点、组件、客户端和容器**四个模块。

- **叶节点**和**容器**都是组合树上的对象，他们二者的区别在于，**叶节点**是组合树的最基本的结构，它不拥有子项目；而**容器**则是包含了其他容器或者叶节点的项目，并且它只通过通用的接口与子项目交互且不关心子项目的类型。

- **组件**是一个对象，里面包括了组合树中所有项目的通用接口/方法。

- **客户端**可以通过组件中声明的通用接口与所有项目进行交互，并且能从容器获取返回的数据。

- 这里为了偷懒就直接用Refactoringguru中的产品与盒子的例子来写代码好惹。首先先声明接口组件和叶结点类`Dot`，并且给这个类设置`name`和`value`属性，然后声明`showValue`方法。

```
interface Package {
    showValue();
}

class Dot implements Package {
    public name: string;
    public value: number;
    constructor(value, name) {
        this.value = value;
        this.name = name;
    }
    showValue() {
        console.log(`${this.name}'Value = ¥${this.value}`);
        return this.value;
    }
} 

```

- 然后是容器，我们声明为`SmallBox`类，也是按照组件接口来声明`showValue`方法，并且因为是容器，除了要在内部维护一个存储子容器或者叶节点的数组，而且要给这个类添加增添子项目对象和删除子项目对象的方法。

```
class SmallBox implements Package {
    public items: Dot[] = [];
    /*--向容器内添加子项目--*/
    add(item: Dot) {
        this.items.push(item);
    }
    /*--在容器内删除子项目--*/
    remove(name: string) {
        for(let i in this.items) {
            if(this.items[i].name = name) {
                this.items.splice(Number(i), 1);
            }
        }
    }

    showValue() {
        let tempSum = 0;
        for(let i in this.items) {
            tempSum += this.items[i].showValue();
        }
        console.log(`SmallBox sum = ¥${tempSum}`)
        return tempSum;
    }
}
```

- 最后来看一下代码的整体效果：

```
let aBox = new SmallBox();
let x = new Dot(500, 'Phone');
let y = new Dot(200, 'Shoes');
y.showValue(); // Shoes'Value = ¥200

aBox.add(x);
aBox.add(y);
aBox.showValue();
/* 
 Phone'Value = ¥500
 Shoes'Value = ¥200
 SmallBox sum = ¥700
*/
```

<center>![2.png](https://i.loli.net/2020/06/24/XfZuvB2YHtqsDdr.png)</center>


二、结语
-------------------------

- 要在实际生产中运用组合模式，首先要确认这些组件是否拥有相同的特质可以整合成相同的接口或者方法，当他们拥有相同的接口的时候，就可以通过组合模式将他们生成一棵组合树并构建树状嵌套递归对象结构，之后便可以通过递归来操作或者获取某一子项目的信息。

- 下篇预告：责任链模式~~🐦咕咕咕咕~~

- 参考资料
    - https://refactoringguru.cn/design-patterns/composite
<br>

上传于2020-06-24


  [1]: https://zh.wikipedia.org/zh-cn/%E4%BA%AB%E5%85%83%E6%A8%A1%E5%BC%8F