---
title: Jquery DOM元素插入
tags: 
      - Jquery
      - 前端
---

Jquery DOM元素插入
=================================

一、
--------------------------
 - 每次在做东西的时候，总要用一点异步后获取的数据来添加dom，这个时候就体现出 JQuery 的强大所在了，它内置了**append()**, **prepend()**, **after()**, **before()**四个神奇的JQ方法来进行DOM元素的添加 <!--more-->
 
二、
--------------------

#1、**append()**

- 首先介绍的是 **append()**
<br>
-  **append()**函数用于将每个匹配元素内部的末尾位置追加内容，与其有相同功能的还有**appendTo()**：

``` html
	<p>1</p>
	<div>2</div>

<!--注意appendTo()和append()的区别-->
<script type="text/javascript">
	$("p").append('<b>666</b>');
	$('<b>999</b>').appendTo('div');
</script>
```
 效果如下：
 ![QQ图片20180426205442.png-2.1kB][1]
 ![QQ图片20180426210325.png-3.1kB][2]
 
元素插入如下：
 ![QQ截图20180426210007.png-27.9kB][3]

 - 可见**append()**是将\$B追加到\$A中：
\$A.append( \$B );  返回\$A

 - 而**appendTo()**是将 \$A 追加到\$B中:
\$A.appendTo(\$B );  返回表示追加内容的jQuery对象( 匹配所有\$B内部末尾追加的\$A )

#2、**prepend()**

- 第二个要介绍的是**prepend()**：
<br>
- **prepend()**函数用于将每个匹配元素内部的起始位置追加内容,与其有相同功能的还有**prependTo()**，示例代码沿用1的：
```
	<p>1</p>
	<div>2</div>

<!--注意prependTo()和prepend()的区别-->
<script type="text/javascript">
	$("p").prepend('<b>666</b>');
	$('<b>999</b>').prependTo('div');
</script>
```
效果如下：
![QQ截图20180426210537.png-1.8kB][4]
![QQ图片20180426210400.png-3.6kB][5]

元素插入如下：
![QQ图片20180426210719.png-31.4kB][6]
 

- 可见prepend()将\$B追加到\$A中
\$A.prepend( \$B ); 返回\$A
- 而prependTo()是将$A追加到\$B中
\$A.prependTo( \$B );  返回表示追加内容的jQuery对象( 匹配所有\$B内部开头追加的\$A元素 )

#3、**after()**

- 第三个要介绍的是**after()**：

- **after()**函数用于在每个匹配元素之后的位置插入内容，与其有相同功能的函数还有 **insertAfter()**。示例：

```
	<p>1</p>
	<div>2</div>

<!--注意prependTo()和prepend()的区别-->
<!--注意after()和insertAfter的区别-->
<script type="text/javascript">
	$("p").after('<b>666</b>');
	$('<b>999</b>').insertAfter('div');
</script>
```

-  效果如下：
![QQ图片20180505221201.png-2.6kB][7]

![QQ图片20180505221446.png-2.5kB][8]

元素插入：

![QQ图片20180505221648.png-34.1kB][9]

- 可见**after()**是将\$B插入到\$A之后
\$A.after( \$B ); 返回\$A
- 而**insertAfter()**是将\$A插入到\$B之后
\$A.insertAfter( \$B ); 返回表示插入内容的jQuery对象( 匹配所有\$B之后插入的\$A元素 )

#4、**before()**

- 第四个要介绍的是**before()**：

- **before()**函数用于在每个匹配元素之前的位置插入内容，与其有相同功能的函数还有 **insertBefore()**。示例：

![QQ截图20180505222033.png-2.8kB][10]

![QQ图片20180505222044.png-2.5kB][11]

元素插入：
![QQ图片20180505222529.png-35.2kB][12]


- 可见**before()**是将\$B插入到\$A之前
\$A.after( \$B ); 返回\$A
- 而**insertBefore()**是将\$A插入到\$B之前
\$A.insertAfter( \$B ); 返回表示插入内容的jQuery对象( 匹配所有\$B之前插入的\$A元素 )


   三、小总结
----------------------------------------  
- 忙碌之余将这个把我有点搞蒙的四个JQ方法小总结一下，虽然当时用的时候有点懵，但是现在用得多了一些之后比以前好多了。不过还是想记录在自己的博客上方便自己哪个时候脑短路的时候拿出来看看。

- 其实要写的东西还有蛮多，不过感觉有些无从下笔，等哪天脑袋清醒一点再写点东西吧。




 


  


  [1]: http://static.zybuluo.com/feiyyx/3vktmr47r0lj3znb938h5c1e/QQ%E5%9B%BE%E7%89%8720180426205442.png
  [2]: http://static.zybuluo.com/feiyyx/w3mmh3ue9p3waoj3bf6gpeo4/QQ%E5%9B%BE%E7%89%8720180426210325.png
  [3]: http://static.zybuluo.com/feiyyx/m8z0trdpeod5bywd0eq2t4bk/QQ%E6%88%AA%E5%9B%BE20180426210007.png
  [4]: http://static.zybuluo.com/feiyyx/ehtqfnpp1c4egg5am7thkspr/QQ%E6%88%AA%E5%9B%BE20180426210537.png
  [5]: http://static.zybuluo.com/feiyyx/k7v46m63ji4kqspyrg3pdsh0/QQ%E5%9B%BE%E7%89%8720180426210400.png
  [6]: http://static.zybuluo.com/feiyyx/tbrng1eznrai8jw0062qmj7y/QQ%E5%9B%BE%E7%89%8720180426210719.png
  [7]: http://static.zybuluo.com/feiyyx/eqfqjb616athpysgtzcbqvoc/QQ%E5%9B%BE%E7%89%8720180505221201.png
  [8]: http://static.zybuluo.com/feiyyx/qw9s7vdo7lprt75kooxpeddf/QQ%E5%9B%BE%E7%89%8720180505221446.png
  [9]: http://static.zybuluo.com/feiyyx/yg02hnel0fgnhgza7iqd06bn/QQ%E5%9B%BE%E7%89%8720180505221648.png
  [10]: http://static.zybuluo.com/feiyyx/m2ad05bta5j35cynm9k0n574/QQ%E6%88%AA%E5%9B%BE20180505222033.png
  [11]: http://static.zybuluo.com/feiyyx/rcfto3kv4lyfqdfejk1ldnj5/QQ%E5%9B%BE%E7%89%8720180505222044.png
  [12]: http://static.zybuluo.com/feiyyx/uuoqjgvsaueaoiixrviqql01/QQ%E5%9B%BE%E7%89%8720180505222529.png
  [13]: https://dsb123dsb.github.io/2016/09/22/%E4%BA%8B%E4%BB%B6%E5%A7%94%E6%89%98%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86%E5%92%8C%E4%BD%BF%E7%94%A8%EF%BC%88js%EF%BC%8Cjquery%EF%BC%89/