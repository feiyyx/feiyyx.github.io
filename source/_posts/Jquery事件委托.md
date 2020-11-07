---
title: Jquery 事件委托
tags: 
      - Jquery
      - 前端
---

Jquery 事件委托
=================================

一、故事背景
--------------------------
 - 在做一个项目的时候，遇到了一个大问题，那就是 AJAX 异步请求后出现的页面并不能使用预先加载好的js脚本。这是为什么呢？
 
 - 这就要从 HTML 的执行顺序说起了。由于 HTML 的执行顺序是从上至下依次加载标签，所以在 AJAX 异步请求后，页面异步分离加载，而 AJAX 载入新 DOM 之前，原先就处于 HTML 中的 JavaScript 就加载完了，所以之前 JS 中的事件就没有绑定到新载入的 DOM 上。<!--more-->
 
二、如何解决
--------------------
- 那么如何解决该问题呢？这就要请出我们的主角——事件委托了。
<br>
-  Q：什么是事件委托？
-  A：事件委托是利用事件冒泡，只指定一个事件处理程序来管理某一类型的所有事件。
<br>
- 一般来说，事件委托多用于避免对特定的每个节点添加事件监听器；相反，事件监听器是被添加到它们的父元素上。事件监听器会分析从子元素冒泡上来的事件，找到哪个是子元素的事件。这样就不会写过多的循环使得页面加载过慢。不过今天我是它拿来解决 AJAX 异步加载后的 JS 执行问题。

- 举个栗子吧，我先创建两个 HTML 页面，带有&lt;html&gt;、&lt;head&gt;和&lt;body&gt;标签的为主页面，命名为omg1.html。

``` html
<!DOCTYPE HTML>
<html>
<head>
<style>
	img {
			border: medium double black;
			padding: 5px;
			margin: 5px;
		}
</style>
</head>
<body>
	<img id="imgtarget" src="./images/huaji.png"/>
	<div id="change">
		<button id="fanu">fanu</button>
	</div>
	<script src="./js/jquery-3.2.1.min.js"></script>
	<script>
		$(document).ready(function(){
			$("#fanu").on("click", function(event) {
 	 			$("#imgtarget").attr("src", "./images/fanu.png");
 	 			$("#change").load("./omg2.html");
 	 	});
			$("#huaji").on("click", function(event) {
 	 			$("#imgtarget").attr("src", "./images/huaji.png");
 	 	});
	});
	</script>
</body>
</html>
```
 - 长得像这样
![QQ图片20180401213634.png-10.4kB][1]
 - 然后再做一个ajax分页面，命名为omg2.html。

```
<button id="fanu">fanu</button>
<button id="huaji">huaji</button>
```
 - 根据我们写的 JQ 函数，点击 [fanu] 后会变成这样：
 ![QQ图片20180401213754.png-10.9kB][2]
 - BUT！根据我们的 JQ函数~~（再一次根据)~~ 再次点击 [huaji] 或者 [fanu] 图标也是会变的，但是这个时候无论怎么点都没有屁用！

 - 这个时候就要请出我们的事件委托了，看似很高深，其实 Jquery 的 on() 函数已经自带了，我们只需修改其中一句，并加上一句变成：
 - 
```
$("#change").on("click", "#huaji", function(event) {
 	 			$("#imgtarget").attr("src", "./images/huaji.png");
 	 	});

$("#change").on("click", "#fanu", function(event) {
 	 			$("#imgtarget").attr("src", "./images/fanu.png");
 	 	});
```
 - 我们就大功告成啦！现在想怎么点就怎么点，滑稽和发怒无缝切换！
 
   三、小总结
----------------------------------------  
- 事件委托进行监听冒泡的是父元素，而不是你定义的异步加载到该界面的子元素。

- 这里有一篇关于这个事件委托的文章，我觉得说的还挺好的
[事件委托技术原理和使用（js，jquery —— From XX雨中人's Blog）][3]
<br>
- 其实写项目的时候遇到了一万个问题，现在也只能挑点比较重大卡了我比较久的大问题来写一写捋一捋思路。也不知道自己究竟能不能完成这个项目，只能说尽全力吧，不让自己后悔，不辜负学长的信任就行！

- 最后附上我最喜欢的一句话，DOTA2骷髅射手之铭言~~：

```
    与其感慨路难行，不如马上出发。
Better to run than curse the road.
```
~~夜神夜神夜神~~
<br>
-  最后附上小玩意的链接：http://fe1yyx.coding.me/SmallThings/omg1.html



 


  


  [1]: http://static.zybuluo.com/feiyyx/1lqfpon44yhl1mpy8axc5xoa/QQ%E5%9B%BE%E7%89%8720180401213634.png
  [2]: http://static.zybuluo.com/feiyyx/8l9k2ttm2mcbl62a0c966a0g/QQ%E5%9B%BE%E7%89%8720180401213754.png
  [3]: https://dsb123dsb.github.io/2016/09/22/%E4%BA%8B%E4%BB%B6%E5%A7%94%E6%89%98%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86%E5%92%8C%E4%BD%BF%E7%94%A8%EF%BC%88js%EF%BC%8Cjquery%EF%BC%89/