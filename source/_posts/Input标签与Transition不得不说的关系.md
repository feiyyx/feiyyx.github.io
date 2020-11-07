---
title: Input标签与Transition不得不说的关系
tags: 
      - 前端
      - CSS
---

Input标签与Transition不得不说的关系
=================================

〇、EMmmmm
-------------------------

- 在写QQ音乐主页的时候，我以为它的那个搜索栏是用了JS实现这个功能

![优酷录屏2018-1_clip.gif-80.4kB][1]

- 结果当我按下f12看源码的时候，发现这个玩意是个纯粹的CSS，那自然是不能忍了，学特么的骚操作好吧！
<!--more-->
一、问题思考
-----------------------------

- 由f12后可以得知，qq音乐的前端是用了:hover选择器和transition方法实现。然后就要解决transition的默认设置问题——当对一个元素使用transition的来扩大时候，它默认是在height方向上从上到下展开，在width方向上从左至右展开。所以我们需要克服这个transition的默认设置。

二、解决过程
--------------------

- 求助万能的谷歌之后以及，我发现在该情境下，想要**完美**解决这个问题只有一种方法：将想要向右展开的元素设置为
```
position: absolute;
right: ***px;
```
- 这样就能像QQ音乐的主页一样秀出骚操作了，不过这样的基础是建立在元素是position：absolute的定位上。

<iframe height='265' scrolling='no' title='如何让transition向左移动' src='//codepen.io/feiyyx/embed/EOOwvO/?height=265&theme-id=0&default-tab=html,result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/feiyyx/pen/EOOwvO/'>如何让transition向左移动</a> by feiyyx (<a href='https://codepen.io/feiyyx'>@feiyyx</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

- 之前我想使用flex布局来实现，这个时候我发现：无论使用什么方法都只会使得这个input标签向右展开，而当我尝试了各种办法之后，发现一个不完全体的骚操作：使用tranform: tanslateX(-100%)

- W3School中给出的定义：transform 属性向元素应用 2D 或 3D 转换。该属性允许我们对元素进行旋转、缩放、移动或倾斜。

- 所以tranform: tanslateX(-100%) 这句CSS的意思是对元素进行-100%的X轴向位置上的转换（一般元素的原点默认在元素最左上角那个点），所以元素的X轴方向上的位置就会进行水平翻转（但不影响文字的顺序），但是这样有一个弊端，在向左展开的时候会鬼畜的抖动，目前我还没发现解决的方法。。。但是不失为一个思路，所以我把它写了上来。下面是代码和演示：

<iframe height='265' scrolling='no' title='Input向左展开2' src='//codepen.io/feiyyx/embed/xQejrx/?height=265&theme-id=0&default-tab=css,result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/feiyyx/pen/xQejrx/'>Input向左展开2</a> by feiyyx (<a href='https://codepen.io/feiyyx'>@feiyyx</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

三、小总结
---------------------------------------

- CSS的动画效果还挺好玩的，以后尽量在自己xjb玩的项目中多实践实践好了，感觉这方面要做深了应该很有意思的。



上传于2018-12-04


 


  [1]: http://static.zybuluo.com/feiyyx/jkz07vl9ne369pxpbwmnk5z9/%E4%BC%98%E9%85%B7%E5%BD%95%E5%B1%8F2018-1_clip.gif