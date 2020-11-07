---
title: CSS隐藏滚动条方法总结
tags: 
      - 前端
      - CSS
---

CSS隐藏滚动条的方法总结
=================================

一、前言
-------------------------

- 最近在自己的deemo里碰到了这个需求，为了美观，所以必须要将`overflow-y:scroll`上的滚动条给隐藏起来，不然这么小的选择栏有个这么大的滚动条，太呆了。

- 于是我就上网查了一些资料，发现主要有两种办法，第一种方法又有很多种实现的方法，于是我深入研究了一番～。<!--more-->

二、实验开始
--------------------------

### 通过控制容器的可视范围隐藏CSS

- 第一个方法在容器之外嵌套再一个div容器，然后给这个父div容器设定`overflow:hidden`，隐藏并阻止父容器的滚动。

- 之后再对子容器的宽高进行自适应，即都等于父容器的100%，之后对可视范围进行处理即可。不过在讲方法前我们先要懂原理，于是我测试了一下滚动条的出现位置和所占空间，以及查询了资料。

- 经过我的测试和网上资料的讲解，滚动条总是出现在会发生滚动的容器的右侧或者下侧（~~废话~~），以及我们熟悉的普通页面发生的滚动是发生在<html>标签上的，也就是说<html>标签和其他标签对比是这样的：

```CSS
/* html默认overflow样式 */
html {
  overflow: scroll;
}

/* 其他标签的默认overflow样式 */
*(HTML除外) {
  overflow: visible;
}
```

- 并且还有一点，这个滚动条会占掉容器右侧大概17px的空间，而且是直接塞在容器里，而且其宽度是计入width的范围内的（但是不额外增加width），也就说假设有一个样式是这样的容器:

```CSS
.container {
    height: 300px;
    width: 200px;
    overflow: scroll;
}
```

- ![1.png-282.6kB][1]

- 然后我们使得内容高度超过300px，之后浏览器发现该容器`overflow`的属性是scroll，然后会给当前容器强行附加一个浏览器内置的滚动条，然后我们打开f12查看width减少了15px，但是实际上盒子在屏幕上的显示宽度整体并没有减少：

- ![图片][2]

- 这是为什么呢？我探索了一下背后的原因，虽然不知道底层是怎么实现的，但是滚动条给我最直观的样式感受是这样的：

```
.scroll-bar {
    position: sticky;
    float: right;
    top: 0;
    right: 0;
    width: 17px;
    height:100%;
    z-index: (我也不知道是多少，反正比容器高XD);
    ...
    /* 其他样式 */
}
```

- 为什么会是`position: sticky`和`float: right`两者呢，因为滚动条是会随着宽度的变化而变化自己的位置，也就是吸附在容器右侧的同时也还保有一定的流动性，并且总是相对位于右侧的元素最先渲染。（**注：每次滚动条生成都会将使得原先在右侧的元素往左边偏移，也就是把右侧的元素向左边挤了，所以它既有定位属性，又有流动属性**）

- 不过虽然在可视数值上不属于该容器，但实际上滚动条仍然属于该容器的一部分。

- 知道这一点后，我们就可以利用这个特性，让滚动条“流”出我们容器的可视范围（**所以这种方法的实现前提是该容器不需要横向滚动**），利用CSS中的调整元素总宽度的几种方法就可以去实现：


#### 通过调整width实现

- 利用CSS3中新增的calc属性，将子容器的width改成`width：calc(100%+17px)`

#### 通过调整padding-right实现

- 若是想要能够兼容性强一些，则可以在子容器中`width:100%`继承父容器宽度的基础上使用`padding-right:17px`实现

### 通浏览器过私有属性实现

- 这种方法最简单的了，使用私有属性的话，让我们来看一下各浏览器之间的方法：

- Chrome
```
.container::-webkit-scrollbar {
    display: none;
}
```

- Edge/IE
```
.container {
    -ms-overflow-style: none;
}
```

- Firefox
```
.container {
    scrollbar-width: none
}

```

#### 上面两种方法汇总：

<iframe height="265" style="width: 100%;" scrolling="no" title="CSS scrollbar hidden" src="//codepen.io/feiyyx/embed/jjRJEw/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/feiyyx/pen/jjRJEw/'>CSS scrollbar hidden</a> by feiyyx
  (<a href='https://codepen.io/feiyyx'>@feiyyx</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>



三、尾声
----------------------

- CSS的魔法真的是神奇呀，随着CSS3标准的不断扩充，以及各浏览器对新CSS方法的支持的增加，现在的CSS已经可以完成很多以前必须借助于JS的才能实现的功能和动画，学习之路任重而道远鸭……

- 以及袁川老师真的太吊惹，把我看呆啦，这个[矩阵雨][3]太🐂🍺惹。看了这么酷炫的前端，谁会不心生向往之情呢。希望有一天我也能炫技嗷。

- 若有错误，请求各位大佬斧正，万分感谢。

- 借鉴资料：
    - https://developer.mozilla.org/en-US/docs/Web/CSS/scrollbar-width
    - https://developer.mozilla.org/zh-CN/docs/Web/CSS/position
    - https://developer.mozilla.org/zh-CN/docs/Web/CSS/overflow
    - https://developer.mozilla.org/zh-CN/docs/CSS/float
    - https://developer.mozilla.org/zh-CN/docs/Web/CSS/calc
    - https://segmentfault.com/q/1010000006863477

<br>
上传于2019-07-13


  


  [1]: http://static.zybuluo.com/feiyyx/1jx6zv5xsn9esjlac73mfuw9/1.png
  [2]: http://static.zybuluo.com/feiyyx/a5wvkh7l5wxm49vp54y273w7/2.png
  [3]: https://codepen.io/yuanchuan/pen/YoqWeR