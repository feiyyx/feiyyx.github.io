---
title: .a.b选择器——CSS新特性使用小技巧
tags: 
      - 前端
      - CSS
---

.a.b选择器——CSS新特性使用小技巧
=================================

〇、楔子
-------------------------
- 今天对着书上敲例程的时候，遇到了一个神奇的带后缀的CSS选择器和神奇的class，它们长这样：<!--more-->

![QQ图片20181101183147.png-44.7kB][1]
![QQ截图20181101183202.png-35.6kB][2]

- 一开始我还以为这个on是单独用来给:class赋值的，但是转念一想不太对啊，后边的 type === 'recommend' 返回的是True或者False，无法成为一个有效的Class名称啊，搜索了一下也没搜索出个所以然（也许是我没搜对关键词），于是去问了下盘神：

![QQ图片20181101183052.png-136.2kB][3]

- OK，那我就测试一下，实践出真知嘛。

一、测试
--------------------------

- 我们先随便写点html：
```
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <title>Test</title>
    <style type="text/css">
        .a {
            color: red;
        }
        
        .a.b {
            border: 1px solid #ccc;
        }
    </style>
</head>

<body>
    <div class="a">
        嘿嘿嘿
    </div>
</body>
</html>
```
效果如下：
![4.png-37.3kB][4]

- 然后我们将代码修改一下，改成：

```
……
<!--加上一个b，前边有个空格~-->
    <div class="a b">
        嘿嘿嘿
    </div>
……
```

可以发现“嘿嘿嘿”周围加上了红色方框，并且在f12中也能看见.a.b这一样式：

![5.png-40.8kB][5]

- 然后当我们删去前边的“a”样式的时候，就变回了默认样式（也就是什么样式都没有）：

![6.png-22.2kB][6]

二、总结
-----------------------------------

- 通过亲自动手感受了一下这个选择器，我也知道了它的妙用，这也就是我的标题的由来。

- 我还记得我以前写项目的时候，用的是很蠢的方法，即一个触发状态用一个新的style，但是这样的话要写很多注释来告诉以后的自己这个是哪一个功能的触发状态，会很不方便，而且还不太好看。

- 而使用这个的话我们可以将“.a.b”抽象成为“.tag.active”——这样的话就会比较直观——在写某一tag的样式的时候，若它有功能需要写触发后的style，那么就可以在.tag的style后面加上一个.tag.active来按上扳机（我个人喜欢把这个功能形容为扳机触发），这样就可以在dom中的class属性里操作来判定是否触发“扳机"的条件，若触发了，则加上.active这个class。

- 并且这种情况下，.active是可以大量重名的，这样就可以统一命名，然后很直观的开发了。
<br>

上传于2018-11-01


  [1]: http://static.zybuluo.com/feiyyx/g68vj79neh4jz5xqoa0d4g33/QQ%E5%9B%BE%E7%89%8720181101183147.png
  [2]: http://static.zybuluo.com/feiyyx/0zz6fw3xuanaiy0106qrzc7w/QQ%E6%88%AA%E5%9B%BE20181101183202.png
  [3]: http://static.zybuluo.com/feiyyx/0gxnpwunjfwy7ws3igm2glzt/QQ%E5%9B%BE%E7%89%8720181101183052.png
  [4]: http://static.zybuluo.com/feiyyx/e1z077w1j4imj1go2jdw3for/4.png
  [5]: http://static.zybuluo.com/feiyyx/isnr0dib4jnw7u76suyzs888/5.png
  [6]: http://static.zybuluo.com/feiyyx/u6sww8eklqpfy2pm3y0q5rkc/6.png