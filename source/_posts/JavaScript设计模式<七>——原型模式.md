---
title: JavaScript设计模式<七>——原型模式
tags: 
      - JavaScript
      - 设计模式
      - TypeScript
---

JavaScript设计模式<七>——原型模式
=================================

〇、前言&介绍
--------------

- 这一次带来的是原型模式的学习。<!--more-->

- 什么是原型模式？*原型模式是创建型模式的一种，其特点在于通过“复制”一个已经存在的实例来返回新的实例,而不是新建实例。被复制的实例就是我们所称的“原型”，这个原型是可定制的。原型模式多用于创建复杂的或者耗时的实例，因为这种情况下，复制一个已经存在的实例使程序运行更高效；或者创建值相等，只是命名不一样的同类数据*。—摘自[维基百科][1]

- 这一模式要求作为原型的对象实现一个可以克隆自身的接口，有了这个接口后我们就可以直接复制一个一模一样的对象，省时省力省心。下面我们就用代码来康康这个模式的妙处，let's show code~

<center>![1.gif](https://i.loli.net/2020/01/15/27gQ1TCi9L6yVtc.gif)</center>


一、分析过程
-------------------------

- 原型模式对于TS与JS来说是较为简单的，因为自带原型链且ES6后又有较多的语法糖，所以实现起来相比其他语言要简单一些；其为数不多的难点在于拷贝属性的时候对于非基础类型的处理，我这里只做了很简单的处理，无法处理环和以`Symbol`作为key的对象，更🐂🍺一点的就看lodash里的源代码好了。

```
interface Clonable<T>{
    clone(): T;
} 

class Prototype implements Clonable<Prototype> {
    public primitive: (string|number|boolean|symbol|null|undefined)
    public obj: object
    clone():Prototype {
        let target = new Prototype();
        target.obj = cloneProperties(this.obj);
        target.primitive = cloneProperties(this.primitive);
        return target;
    }
}

let cloneProperties = (prop):any => {
    let _newProp = null;
    if(typeof prop !== 'object' || prop === null || prop === undefined) {
        return prop;
    } else {
        switch(Object.prototype.toString.call(prop).substring(8).replace(']',"")) {
            case 'Array':
                _newProp = [];
                for(let key in prop) {
                    typeof prop[key] === 'object'? _newProp[key] = cloneProperties(prop[key]) : _newProp[key] = prop[key];
                }
                break;
            case 'Object':
                _newProp = {};
                for(let key in prop) {
                    typeof prop[key] === 'object'? _newProp[key] = cloneProperties(prop[key]) : _newProp[key] = prop[key];
                }
                break;
            case 'Date':
                 _newProp = new Date();
                _newProp.setTime(prop.getTime());
                break;
            default:    
                throw new Error("Weird Prop!")
        }   
    }
    return _newProp;
}
```

- 这次代码偏少，我就把TS编译后的也贴出来吧：

```
/*TypeScript编译后得到的Ja v aS*/
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

- JavaScript（ES6）版的话就显得更简单，也顺带贴一下吧：

```
class Prototype {
    primitive = null
    obj = null
    clone(){
        let target = new Prototype();
        target.obj = cloneProperties(this.obj);
        target.primitive = cloneProperties(this.primitive);
        return target;
    }
}
```

- 来用简单的例子来测试一下结果：

```JavaScript
let x1 = new Prototype();
x1.primitive = Symbol(5);
x1.obj = {a:1,b:{c:2}}

let x2 = x1.clone();
console.log(x1.__proto__ === x2.__proto__ ? "Class Copy Success!" : "Class Copy Failure!")
// "Class Copy Success!"

console.log(x1.primitive === x2.primitive ? "Primitive Copy Success!" : "Primitive Copy Failure!")
// "Primitive Copy Success!" 

console.log(x1.obj === x2.obj ? "Object Copy Failure!" : x1.obj.b === x2.obj.b ? "Object Copy Failure!" : "Object Copy Success!")
// "Object Copy Success!"
```

<center>![3.png](https://i.loli.net/2020/01/15/6JqsW7feHIdky1F.png)</center>


二、结语
-------------------------

- 当对象十分复杂的时候，使用原型模式来生成克隆一个新实例比使用构造函数new更加方便，因为复制一个已经存在的实例会比重新构造一个实例所花费的资源与时间少；还有一种情况就是当需求中需要使用预设的对象时，可以只通过对这些对象原型进行克隆来获得实例，以此来减少构造新实例所需的时间。

- 下篇预告：生成器模式~~🐦咕咕咕咕~~

- 参考资料
    - https://refactoringguru.cn/design-patterns/prototype
    - https://blog.csdn.net/zhengzhb/article/details/7393528
<br>

上传于2020-01-15


  [1]: https://zh.wikipedia.org/zh-hans/%E5%8E%9F%E5%9E%8B%E6%A8%A1%E5%BC%8F