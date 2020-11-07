---
title: JavaScript 关于闭包的学习心得体会
tags: 
      - JavaScript
      - 前端
---

闭包(closure)
=================================

一、闭包的介绍
--------------------------
 - 函数对象可以通过作用域链互相关联起来，函数体内部的变量都可以保存在函数作用域内，这种特性在计算机科学文献中称为“闭包”。                                                               <!--more-->
 - 从技术的角度讲，所有的JavaScript函数都是闭包：它们都是对象，它们都关联到作用域链。定义大多数函数时的作用域链在调用函数时依然有效，但这并不影响闭包。
——《JavaScript 权威指南》
 - 由此可见，想要了解闭包的操作方法，首先要了解JavaScript的作用域以及它的作用域链的工作原理，这里给一个我认为讲得还不错的博客链接：https://gaohaoyang.github.io/2015/05/20/scope/ 或者去翻阅《JavaScript 权威指南》第3.10和3.10.3节中关于这两者的内容。只有充分理解了JavaScript的作用域以及它的作用域链的工作原理才能弄懂 JavaScript 中闭包的工作原理

 
二、关于闭包的理解
--------------------
 - 闭包在JavaScript的最大用处有两个，一个是可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中而不会被当做垃圾回收。
 - 在看过[阮一峰先生][1]关于闭包的解释后，结合他给的思想题的输出结果，再反复翻阅了《JavaScript 权威指南》关于闭包的这一章节后，我对于闭包有了自己的认识。
 - 首先构造个和他博客中所给思考题1中相似的函数：
 ```JavaScript
var name = "Window"
var object = {
name : "Object",
getName : function(){
console.log(this);   //加入这行代码以显示调用函数的对象。
return function() {
console.log(this);  //加入这行代码以显示调用函数的对象
return this.name;
};
}
};
```
输出结果如下：
![20171220-211539.jpg-22.2kB][2]
 - 第一条 console.log 输出对象的是 object，也就是说运行到那个函数时，是 object 调用的它，这是肯定的，毕竟这个函数就是设定为 object 的一个属性。
 - 到了第二条 console.log 输出的是 Window，也就是说函数里的return 返回的值被全局对象调用，进入到了全局对象的环境中。
 - 然后经我的测试只要该函数的返回值在全局作用域链上（无论是经过嵌套函数 return 返回至全局对象 Window 上，又或者函数是被作为一个值被赋给在全局作用域内的变量：
![20171225-104846.jpg-30.7kB][3]
该函数的变量的值始终保持在内存中而不会被当做垃圾回收掉，且也能被外部读取。这样就形成了闭包。
 三、小结
----------------------------------------  
- 这些也只是我自己对于闭包的理解，如果有什么不对的地方请发送邮件到我的邮箱 feiyy2008@qq.com 中指正，对于大佬们的斧正我可是期待满满\_(:3 」∠)_。
<br>
- 参考资料
    - 《JavaScript 权威指南》第 8.6 节
    - [学习Javascript闭包（Closure） ——阮一峰][4]


  [1]: http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html
  [2]: http://static.zybuluo.com/feiyyx/nnrkn5ejj7x5ju5criac0xfq/20171220-211539.jpg
  [3]: http://static.zybuluo.com/feiyyx/skznsg0zi7x10wpzut94nagp/20171225-104846.jpg
  [4]: http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html