---
title: JavaScript设计模式<十三>——代理模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<十三>——代理模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是代理模式的学习。<!--more-->

- 什么是代理模式？根据[维基百科][1]：*代理模式是程序设计中的一种设计模式。 所谓的代理者是指一个类别可以作为其它东西的接口。代理者可以作任何东西的接口：网络连接、存储器中的大对象、文件或其它昂贵或无法复制的资源。*。

- 也就是说，代理模式是由一个第三方作为中介，还原了服务端的接口；这个第三方只是为了达到某种目的（例如节省资源等）而存在的并且只是复制了接口，实际上代理者和被代理者是可以互换的，使用上并没有实质性的改变，只是通过代理的中介可以完成一些之前提到的目的而已。

<center>![E8864CCF398D924A0E2B40CA9CE25AF1.gif](https://i.loli.net/2020/03/04/GocKEmzQVACBiON.gif)</center>


一、分析过程
-------------------------

- 举个数据库查询的例子，然后我们扩展一下变成电影的数据库：

```
interface DataBase{
    select(field:string):string;
    delect(field:string):string;
    add(field:string):string;
}

class MoviesDataBase implements DataBase {
    protected movieList:string[] = ["海王", "小丑", "蝙蝠侠"];
    select(field:string):string {
        if(this.movieList.includes(field) === true) {
            return this.movieList[this.movieList.indexOf(field)];
        }
        return "No such movie!";
    }
    delect(field:string):string {
        if(this.movieList.includes(field) === true) {
            this.movieList.splice(this.movieList.indexOf(field),this.movieList.indexOf(field)+1);
            return "Delete Success!"
        }
        return "Delete Failure!"
    }
    add(field:string):string {
        if(this.movieList.includes(field) === false) {
            this.movieList.push(field);
            return "Add Success!"
        }
        return "Add Failure!"
    }
}

```

- 然后我们创建一个`Proxy`代理类来代理这个电影数据库（假装因为这个数据库太庞大链接调用一次耗时过久）

```
class ProxyDataBase implements DataBase {
    private realSubject: DataBase
    constructor(realSubject:DataBase) {
        console.log("Loading...");
        this.realSubject = realSubject;
        console.log("System Loading Success!")
    }
    select(field:string):string {
        return this.realSubject.select(field);
    }
    delete(field:string):string {
        return this.realSubject.delete(field);
    }
    add(field:string):string {
        return this.realSubject.add(field);
    }
}
```

- 可以看到，代理类中的接口和原本的接口几乎一致，然后我们试试效果：

```
let system = new MoviesDataBase();
system.select("小丑"); // "小丑"
system.add("沙赞"); // "沙赞"

let search = new ProxyDataBase(system);
system.select("沙赞"); // "沙赞"
system.delete("沙赞"); // "Delete Success!"
```

<center>![QQ20200304-162727@2x.png](https://i.loli.net/2020/03/04/YTmbV89vODiaQsh.png)</center>

- 代理模式就是这么简单的，一点也不复杂，只需要把它和其他设计模式的异同点厘清就比较好理解了。ES6版本和TS几乎一致，删去interface和类型限制即可。

二、结语
-------------------------

- 虽然代理模式很简单，但是和它相似的设计模式还有很多，比较容易弄混，这里还是借用refactoringguru中的总结，也算是为我自己提个醒：
    - **适配器模式**能为被封装对象提供不同的接口，**代理模式**能为对象提供相同的接口， **装饰模式**则能为对象提供加强的接口。
    - **外观模式**与**代理**的*相似之处*在于它们都缓存了一个复杂实体并自行对其进行初始化。 代理与其服务对象遵循同一接口，使得自己和服务对象可以互换， 在这一点上它与外观不同。
    - 装饰和代理有着相似的结构， 但是其意图却非常不同。这两个模式的构建都基于组合原则，也就是说一个对象应该将部分工作委派给另一个对象。两者之间的不同之处在于**代理通常自行管理其服务对象的生命周期**，而**装饰的生成则总是由客户端进行控制**。并且我个人理解与参考网上其他资料：装饰模式会给原有系统增强其功能；而代理模式则最多增加一层身份校验，而系统其他的地方则完全不做改动。

- 下篇预告：享元模式~~🐦咕咕咕咕~~

- 参考资料
    - https://refactoringguru.cn/design-patterns/proxy
    - https://www.jianshu.com/p/e60b3bd50bc2
<br>

上传于2020-02-26


  [1]: https://zh.wikipedia.org/zh-cn/%E4%BB%A3%E7%90%86%E6%A8%A1%E5%BC%8F