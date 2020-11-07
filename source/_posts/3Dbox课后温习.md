---
title: 3Dbox课后温习
tags: 
      - CSS
      - 前端
---

3D-Box 弹性盒模型入门+CSS+一点点JS
=================================

一、准备工作 
--------------------------


 - 我首先凭着我在网课上对弹性盒模型的记忆撸出了这样的形状：
![此处输入图片的描述][1]


  然后就开始着手回想+收集写出3dbox所需要的代码
 
二、那当然是开始码代码咯
--------------------

-  首先得把那三个串出来的的方块叠在一起，这就需要我们的绝对定位：<!--more-->
```
    position: absolute;
```
在国内辣鸡速成网站查到的解释是这样的：<br><br>
*生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位。
元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。*
<br>

于是就可以出现下面的效果，也就是我们的3d旋转的第一步：将它们全部重叠在一个区域内
![此处输入图片的描述][2]
然后其实到这一步后我忘记改怎么设置坐标系了，于是上网收集了一波资料,要用到我们的relative设置一波坐标系：
```
    position:relative;
```
在国内辣鸡速成网站查到的解释是这样的：<br><br>
*生成相对定位的元素，相对于其正常位置进行定位。*
<br>

设置完坐标系后还要设置景深，不然整个图形就会没有立体感，这时候就用到了景深：perspective
```
    perspective: value px;
```
在国内辣鸡速成网站查到的解释是这样的：<br><br>
*erspective 属性定义 3D 元素距视图的距离，以像素计。该属性允许您改变 3D 元素查看 3D 元素的视图。
当为元素定义 perspective 属性时，其子元素会获得透视效果，而不是元素本身。
注释：perspective 属性只影响 3D 转换元素。*
<br>
目前我还不知道如何确认合适的景深大小，姑且算是先照着网上来了个 1000 px ，效果如下：
![此处输入图片的描述][3]
（我已经将first和third所在页面用**transform: translateY( value px)**改变了其位置，至于坐标系是如何建立的，css中**transform: rotateX(90deg);**旋转的正方向是逆或者顺时针，在后面总结全文的时候我再赘述）
但是这样做之后会因为近大远小的关系而显得整个正方形变得超大，于是我们等比例缩小它们，并使用**top**与**left**（都是沿着相反方向移动）对正方形位置进行调整，最后静止效果和第二张图一样，但是特么的动起来就不一样了……最后发现是景深取小了，放大10倍之后视觉效果就好了，但是需要重新调整大小。
然后因为实在是个JavaScript小白，我就去求助了盘神，最后在盘神的帮（代）助（码）下，再加上他的提示，我勉强算是实现了这个目标，代码比较冗长，轻喷。
```
<script type="text/javascript">
		var angle = 0;


		function myFunction1(){
	
			var thisDiv = document.getElementById("box");

			if(angle == 0){

			angle = angle - 90;

			thisDiv.style.transform = "rotateX(" + angle +"deg)";


		} else if(angle == 90) {

			angle = angle - 180;

			thisDiv.style.transform = "rotateX(" + angle +"deg)";
	
		}


		}

		function myFunction2(){
	
			var thisDiv = document.getElementById("box");

			if(angle == -90){

			angle = angle + 90;

			thisDiv.style.transform = "rotateX(" + angle +"deg)";


		} else if(angle == 90) {

			angle = angle - 90;

			thisDiv.style.transform = "rotateX(" + angle +"deg)";
	
		}


		}

		function myFunction3(){
	
			var thisDiv = document.getElementById("box");

			if(angle == 0){

			angle = angle + 90;

			thisDiv.style.transform = "rotateX(" + angle +"deg)";


		} else if(angle == -90) {

			angle = angle + 180;

			thisDiv.style.transform = "rotateX(" + angle +"deg)";
	
		}


		}

</script>
```

三、一些小总结
----------------------------------------  
- 拥有**absolute**属性的元素遵循后来居上原则（如果有时间我就补补图）
<br>
- **rotateX()**在旋转时，旋转的x轴是从左边看屏幕，沿着轴顺时针旋转。
<br>
- 若只是想让导航栏的文字垂直居中，可以用**line-height: 导航栏高 px**和**text-align: center**;这两个样式，效果还不错
<br>
- 这次上网查资料的时候发现大家都是拿css中的webkit来写旋转，所以等我学到了那一块看看能不能用我自己学的知识来写出纯css的3D旋转，这次还麻烦了国庆在重庆陪女票旅游的盘神，感觉挺过意不去的。说到底的还是自己太菜，这么简单的小玩意居然写了我一个下午+一个晚上，只有再努努力提高自己了，希望一个月过后再看这篇文章能够发现自己是多么的菜。
<br>
-  最后附上小玩意的链接：http://fe1yyx.coding.me/SmallThings/3dbox.html
![此处输入图片的描述][4]

                                                                                 上传于2017-10.01
                                                                              图片补档于2017-10.21

 


  [1]: http://static.zybuluo.com/feiyyx/m62q61hui9kc4m0ovp68fm3i/1.png
  [2]: http://static.zybuluo.com/feiyyx/ew1d7lxv8thrz7ocnuoppq4y/QQ%E6%88%AA%E5%9B%BE20171001163236.png
  [3]: http://static.zybuluo.com/feiyyx/v40egjz5b845jkocj8jce8ul/QQ%E6%88%AA%E5%9B%BE20171001165756.png
  [4]: http://static.zybuluo.com/feiyyx/wzus55g44d236nqwzqsuw787/2017-10-01.png