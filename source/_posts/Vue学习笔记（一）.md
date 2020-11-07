---
title: Vue学习笔记（一）
tags: 
      - Vue
      - 前端
---

Vue学习笔记（一）
=================================

一、
--------------------------
 - 最近在啃电子版的《Vue.js实战》，在敲书上实例的时候看到了这样的例题拓展：**在输入框聚焦时，增加对键盘上下按键的支持，相当于加1和减1**<!--more-->
 - 而原来的题目实现后是这样的：
![1.png-1.1kB][1]
点击+和-能够实现对输入框内的数字进行+1和-1操作，这个拓展题目的意思就是叫我们自己动手在Vue中实现绑定按键。
  
二、
--------------------

### 1、 绑定按键

 - 由于要绑定按键，所以先去网上搜罗了Vue中关于绑定按键的代码：
```
E.G: <input type="text" placeholder="请输入" @keyup="methods(event)"> 

<!--
@keyup.enter 回车(keyCode=13)

@keyup.left 左键(keyCode=37)

@keyup.right 右键(keyCode=39)

@keyup.up 上键(keyCode=38)

@keyup.down 下键(keyCode=40)
-->
```
 - 于是在template内的原标签基础上加上keyup.up和keyup.down：
```
<input type="text" :value="currentValue" @keyup.up= "clickUp" @keyup.down= "clickDown" @change="handleChange" >\
```
 - 之后就是要写调用的函数了。

### 2、Vuejs中methods中的作用域

- 由于之前的代码书上例程已经给好，所以我思考了一下：能不能复用methods中定义好了的方法呢？于是我上网搜索方法调用其他方法的一行代码：
```
Mehod: function() {
this.$options.methods.otherMethod();
}
```
- 凭着自己的理解写出了这样(chun)的代码：
```
...
handleUp: function() {
    if (this.currentValue >= this.max) return;
        this.currentValue += 1;
        console.log(this.currentValue);
        },/*例程所给<button>标签用函数代码*/
clickUp: function(event) {
     if (event.keyCode == 38)
        this.$options.methods.handleUp();
     }/*自己添加的绑定按键代码*/
...
```
- 在我满怀希望会敢敢单单成功的时候，打开console发现跳出来的是：
![2.png-9.7kB][2]
- 此时我的表情是这样的：
![3.jpg-5.5kB][3]
- 为什么会这样？？？我的内心这样呐喊着，思考无果，唯有上网查询才能维持得了生活的样子。查询一番后，得到一条这样的结论：
***methods中的function中的this指向vue实例***
- 看到这句话我就茅塞顿开了，这应该就是为什么没有值传入的原因了，解决方法也很简单，使用**bind()**给调用的method绑定上调用他的method的作用域，也就是将它绑回实例的作用域：
```
ClickUp: function(event) {
    if (event.keyCode == 38)
         this.$options.methods.handleUp.bind(this)();
    },
```
 - 再运行一次
![4.png-7.7kB][4]
可以看到就正常了，又学到了新的姿势~~

三、小总结
----------------------------------------  
- Vue的入门有点门槛，不过我感觉如果能迈过这道坎，之后的运用应该会像一把趁手的武器一样好使。不过我现在还是感觉它有点复杂，不过比JQuery好玩多了2333。




上传于 2018-7.18
图片补档于 2018-8.31


  


  [1]: http://static.zybuluo.com/feiyyx/408e0ninpabymhxdzpi274e3/1.png
  [2]: http://static.zybuluo.com/feiyyx/123kwofsxht3eh7i0sd10br0/2.png
  [3]: http://static.zybuluo.com/feiyyx/b73xrspluwkcgp8lvij1f2fc/3.jpg
  [4]: http://static.zybuluo.com/feiyyx/0bi32kyqlwcocqd3smsub0xk/4.png