---
title: getBoundingClientRect()学习笔记
tags: 
      - 前端
      - JavaScript
---

getBoundingClientRect()学习笔记
=================================

一、前言
-------------------------

- 我曾经想过有没有一个原生的api供我们去判断页面内元素的位置，这样就可以不用调用JQuery的offset().top+scroll()来进行判断，也不用我们自己手写一个根据offsetTop判断的函数了。一次逛论坛的偶然发现，使得我真的就发现了这样神奇的api——getBoundingClientRect()。<!--more-->

- 简单介绍一下这个api：
    - *返回值是一个 DOMRect 对象，这个对象是由该元素的 getClientRects() 方法返回的一组矩形的集合, 即：是与该元素相关的CSS 边框集合* 。

    - *DOMRect 对象包含了一组用于描述边框的只读属性——left、top、right和bottom，单位为像素。除了 width 和 height 外的属性都是相对于视口的左上角位置而言的。*——MDN

- 它的判断相比较CSS里的�又️不太一样，CSS的想法是上下左右各自距离屏幕
进行判断。而这个api是这样的（正如mdn中对它的描述里的最后一句话所说那样）：

![1.png-24.8kB][1]

- getBoundingClientRect()方法能获得元素的如下信息（不只是left、top、right、bottom、width和height，还有x和y，不过IE只有前四种属性）

![2.png-84.2kB][2]

- 下面我们来体会一下它的食用方法：

二、食用方法
-------------
 
- 我们先弄出一个超级高的container以及很显眼的example元素：
```HTML
<div class=container>
  <div class="example">123</div>
</div>
<div class="count">
</div>
```
```SCSS
div, p, span {
  margin: 0;
  padding: 0;
}

.container {
  width:500px;
  height:1000px;
  background-color: #66ccff;
  .example {
    width:100px;
    height:100px;
    background-color: red;
    color: #fff;
    text-align: center;
    line-height: 100px;
  }
}
```
- 效果如下：

![3.png-98.1kB][3]

- 然后加入监听的计数器：

![4.png-30.3kB][4]

- JS代码没啥技术含量，就是对着api一顿输出，复制粘贴+修改，不过使用了loadash的节流函数._throttle()，然后调用window.addEventListener()进行监听即可。

- 以及，这个CodePen的JS不支持用top、bottom等CSS的关键词进行变量命名，给我整了好久才发现……赶紧记下来免得以后又翻车

三、结语
-----------------

- 这篇文章算是我最近今天对于这个api文章的学习和总结吧，最近几天浏览思否和掘金，发现前端还有好多我不懂的东西Orz，学习之路很漫长。

- 这个api的用法主要是拿来监听某些元素：我们需要根据其相对可视页面的位置来对该样式进行改变（增加动画/吸顶等）。不过听说会对性能有一些影响，这个就留到以后工作了再想吧。

- 这个Deemo在这：

<iframe height="265" style="width: 100%;" scrolling="no" title="getBoundingClientRect()" src="//codepen.io/feiyyx/embed/zXmyeG/?height=265&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/feiyyx/pen/zXmyeG/'>getBoundingClientRect()</a> by feiyyx
  (<a href='https://codepen.io/feiyyx'>@feiyyx</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

- 参考资料：
    - [【前端词典】5 种滚动吸顶实现方式的比较\[性能升级版\]][5]
    - [你真的会用getBoundingClientRect吗][6]

<br>
上传于2019-07-05


  [1]: http://static.zybuluo.com/feiyyx/vedot8vcwsz1o3xyjc6epufk/1.png
  [2]: http://static.zybuluo.com/feiyyx/xw7n7aj8upwllpi5lqm57sw2/2.png
  [3]: http://static.zybuluo.com/feiyyx/0s18zjuv0u81tag0ifqsy8s4/3.png
  [4]: http://static.zybuluo.com/feiyyx/7ijhiec6mdim2nualwrwdfuq/4.png
  [5]: https://juejin.im/post/5caa0c2d51882543fa41e478#heading-0
  [6]: https://juejin.im/entry/59c1fd23f265da06594316a9