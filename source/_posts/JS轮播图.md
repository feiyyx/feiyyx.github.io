---
title: JS轮播图
tags: 
      - CSS
      - 前端
      - JQuery
---
 JS轮播图
=================================

一、准备工作 
--------------------------
~~感觉自己学了一些JS有点底气了然后~~收集了一些首页拥有轮播图的网站，并搜索了一些这方面的代码来进行研究学习。~~结果却没怎么用原生JS反倒是JQ用的多。。。因为有些东西原生JS实现起来太麻烦。~~

二、温故知新
-------------------
### 1. CSS篇
 - 虽然都是学过的东西，但是组装起来就没那么简单了，虽然都是懂的代码，但是如果不亲身去敲一下就完全不知道是怎么一回事。<!--more-->
 - ①、像是 **overflow: hidden;** 这个样式，如果不小心扔在了容器里面的盒子的样式上，你就会绝望的发现完全不起效果，最后翻来覆去好长检查一阵子都没发现为什么出错。结果我之前用了半天竟然没弄懂这个是元素自身的属性，这就有点令人伤心。
 - ②、又像是 **float: left;** 这个是赋予元素本身的属性而不是赋予元素内子元素的属性（**display: flex;** 也是)，所以我也弄了一会才把图片给排在一排上拍好，真是感觉自己之前的前端白学了。。。
 - ③、然后到弄左右箭头的时候就又头疼了，这关系到 **position** 的用法，弄了老半天都没看见，最后看了好多代码之后发现一个关键代码：**z-index: value;** ，这个样式的属性是这样的(来自w3school http://www.w3school.com.cn/cssref/pr_pos_z-index.asp)：
 <br>
  - **z-index** 属性设置元素的堆叠顺序。拥有更高堆叠顺序的元素总是会处于堆叠顺序较低的元素的前面。
**注释**：元素可拥有负的 z-index 属性值。
**注释**：Z-index 仅能在定位元素上奏效（例如 position:absolute;）！
说明
该属性设置一个定位元素沿 z 轴的位置，z 轴定义为垂直延伸到显示区的轴。如果为正数，则离用户更近，为负数则表示离用户更远。
用图像来解释就是这样的（字有点丑不要在意）：
![QQ截图20171028084141.png-25.8kB][1]
在使用了这个代码之后并调整相应数值后，我的左右箭头才终于显示出来。
几番调整之后布局如下：
![QQ截图20171028091124.png-1238kB][2]
接下来就要上JS了让轮播图动起来。
p.s. 图片均来自P站

### 2. JavaScript篇
 - 首先先要让图片自己动起来，这个时候我想（看）到了（别人的代码后想起）我刚学完的 setInterval 函数，再加上喜闻乐见的JQuery来实现动画的移动效果。代码如下：
``` JavaScript
var index = 0;

setInterval(function() {

		if (index == 0) {
			$("#sliders").animate({marginLeft : 0}, 800);

			index++;
		} else if (index <= 2) {
			$("#sliders").animate({marginLeft : "-" + (100*index) + "%"}, 800);

		    	$('#carousel').find('span').eq(index).addClass("on").siblings().removeClass("on");
		index++;
	} else {
			$("#sliders").animate({marginLeft : 0}, 1000);
			index = 0;

		    	$('#carousel').find('span').eq(0).addClass("on").siblings().removeClass("on");
		}
	}, 3000);
```
首先引入index来作为图片的索引，第一张图是 **index=0** 然后递增+1，这里只有3张所以对应序号就是0、 1、 2。然后根据索引来作为判断的条件来生成图片水平移动的动画以及索引栏对应亮起来的效果。

- 这时候可以看见图片动起来了，然后我们还需要完成点击底部的索引栏后跳转到相应图片的动画，思考了一会+调试了n久之后，完整代码如下：
``` JavaScript
$('#carousel span').click(function() {
	var list = $(this).index();

		if (list == 0) {
			$("#sliders").animate({marginLeft : 0 }, 800);
			index = list;

		    	$('#carousel').find('span').eq(index).addClass("on").siblings().removeClass("on");

		}  else if (list <= 2) {
			$("#sliders").animate({marginLeft : "-" + 100*list + "%"}, 800);
			index = list;

		    	$('#carousel').find('span').eq(index).addClass("on").siblings().removeClass("on");
		}

	});
```
代码是最终优化（我也不知道用什么词了……）的结果，也许有人说，既然你都有给定的ID和标签了，为什么不用  **\$(this) 来替代 $('#carousel').find('span')**呢？我想说这就是我测试优化的结果，当我使用this时，在Chrome测试效果十分不理想，点击索引栏有很强延迟感，但是当我使用find的时候却很快，目前我所学的东西还不能解释这是为什么，希望以后我能搞明白吧。

- 接下来就是要使得两边的小箭头能够派上用场，有了前面的代码这个就简单了：
``` JavaScript
$("#next").click(function() {
		index++;

		if (index <= 2) {
			$("#sliders").animate({marginLeft : "-" + 100*index + "%"}, 500)

		    	$('#carousel').find('span').eq(index).addClass("on").siblings().removeClass("on");

		} else if(index > 2){
			index = 0;
			$("#sliders").animate({marginLeft : 0}, 800);

		    	$('#carousel').find('span').eq(0).addClass("on").siblings().removeClass("on");
		}
	});

	$("#prev").click(function() {
		index--;

		if (index >= 0) {
			$("#sliders").animate({marginLeft : "-" + 100*index + "%"}, 500)

		    	$('#carousel').find('span').eq(index).addClass("on").siblings().removeClass("on");

		} else if(index < 0){
			index = 2;
			$("#sliders").animate({marginLeft : "-" + 200 + "%"}, 800);

		    	$('#carousel').find('span').eq(2).addClass("on").siblings().removeClass("on");
		}
	});
```
 - 但是我发现一个小bug，就是当你按下按钮的时候如果时间间隔正好与设定的自动播放时间间隔一样，就会一次跳两格，这就有点小尴尬，自己尝试失败后，去搜索了一番如何重启setInterval函数，然后改好后的代码如下：
```JavaScript
function autoPlay() {
		timer = setInterval(function() {
			if (index <= 2) {
				$("#sliders").animate({marginLeft : "-" + (100*index) + "%"}, 800);

				$('#carousel').find('span').eq(index).addClass("on").siblings().removeClass("on");
				index++;
			} else {
				$("#sliders").animate({marginLeft : 0}, 800);
				index = 0;

				$('#carousel').find('span').eq(0).addClass("on").siblings().removeClass("on");
			}
		}, 3000);
	}

	autoPlay();
```
将其变为函数就可以很方便的利用 **clearInterval(timer);** 和 **autoPlay()** 来进行重启，这样就不会发生冲突了。然后在查询的时候正好也查到了一个悬浮暂停的函数，也顺便参考了一波，自己写了一个：
```JavaScript
$("#container").hover(function() {    //这是鼠标移上后生效的函数
	$("#sliders").css("opacity", "0.8");
	clearInterval(timer);
	}, function () {             //这是鼠标离开后生效的函数
	$("#sliders").css("opacity", "1");
		autoPlay();
});
```
最后调试一下程序，修复一些小bug就大功告成啦！
最后老规矩放出链接，由各位自行体会：
戳我→ http://fe1yyx.coding.me/SmallThings/轮播图.html
p.s.轻点玩，现在不是很会调节这个的速度，玩的太快会鬼畜的。。。
 三、后记
--------------------
- 这次虽然也参考了不少别人的代码，但是这些代码都是我看懂之后一个一个子儿敲进来的2333，虽然我也想过直接使用，但是如果直接拿来主义，并且自己也并没有搞清楚里面的运作原理的话，那和不会思考有什么区别呢？所以我都是先看个思路，然后借鉴其中的函数方法，敲一行思考一行，希望未来的自己还能保持这种高风亮洁（误）的精神，并且能够先思考再借鉴代码吧，现在不看思路真的是一点思绪都没有，说白了还是菜（扶胸）。
- 快到万圣节了，抽了三个多小时摸了一条鱼，将就着看吧233
![2017helloween!-min.png-1008.2kB][3]
~~大坑依旧在路上~~


  [1]: http://static.zybuluo.com/feiyyx/vflk7kaw6k1c75ty7bycszog/QQ%E6%88%AA%E5%9B%BE20171028084141.png
  [2]: http://static.zybuluo.com/feiyyx/hpr02dybu7gufekv4obwpuc2/QQ%E6%88%AA%E5%9B%BE20171028091124.png
  [3]: http://static.zybuluo.com/feiyyx/gb0zz8optnkit6cnsdbxu8m4/2017helloween!-min.png