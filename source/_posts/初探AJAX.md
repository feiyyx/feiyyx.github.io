---
title: 初探AJAX
tags: 
      - JavaScript
      - 前端
---
初探AJAX
=================================

一、前言
--------------------------
- ~~都说一流大学抓学术，二流大学抓教育，三流大学抓纪律，辣鸡大学抓生活。看来你校挺符合最后两点的~~最近学校破事不少，再加上最近学的有点杂，不是很好凝练总结，所以过了一段时间才有闲工夫坐下了写点东西记录下过去浅浅接触的AJAX<!--more-->

二、温故知新
-------------------
###AJAX介绍
 - "AJAX即“Asynchronous JavaScript and XML”（异步的JavaScript与XML技术），指的是一套综合了多项技术的浏览器端网页开发技术。"
 - "使用Ajax的最大优点，就是能在不更新整个页面的前提下维护数据。这使得Web应用程序更为迅捷地回应用户动作，并避免了在网络上发送那些没有改变的信息。
Ajax不需要任何浏览器插件，但需要用户允许JavaScript在浏览器上执行。就像DHTML应用程序那样，Ajax应用程序必须在众多不同的浏览器和平台上经过严格的测试。随着Ajax的成熟，一些简化Ajax使用方法的程序库也相继问世。同样，也出现了另一种辅助程序设计的技术，为那些不支持JavaScript的用户提供替代功能。"
 - ——以上摘抄自维基百科

###初次接触AJAX
 - 一开始听到AJAX这个词是在一个视频里，说是可以通过AJAX异步请求达成图片懒加载这一骚操作，正好最近也学到了这个东西，所以自己也尝试着使用了一下。
 - 首先AJAX的核心函数是：
   -  **XMLHttpRequest()**： 用于创建 XMLHttpRequest 对象
XMLHttpRequest 用于在后台与服务器交换数据。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。其语句为：
    >variable=new XMLHttpRequest();

     其中还有一些基础的子函数：
   -   请求类：
     - **open(method,url,async)**	
规定请求的类型、URL 以及是否异步处理请求。
method：请求的类型；GET 或 POST
url：文件在服务器上的位置
async：true（异步）或 false（同步）
     - **send(string)**:
将请求发送到服务器。
string：仅用于 POST 请求 
   - 响应类：
     - **responseText**	获得字符串形式的响应数据。
     - **responseXML**	获得 XML 形式的响应数据。
   - readyState: 
     - **onreadystatechange** 事件
当请求被发送到服务器时，我们需要执行一些基于响应的任务。
每当 readyState 改变时，就会触发 onreadystatechange 事件。
readyState 属性存有 XMLHttpRequest 的状态信息。
下面是 XMLHttpRequest 对象的三个重要的属性：
**onreadystatechange**	
存储函数（或函数名），每当 readyState 属性改变时，就会调用该函数。
**readyState**	
存有 XMLHttpRequest 的状态。从 0 到 4 发生变化。
UNSET 0: 请求未初始
OPENED 1: 服务器连接已建立
HEADERS_RECEIVED 2: 请求已接收
LOADING 3: 请求处理中
DONE 4: 请求已完成，且响应已就绪
**status:**	
200: "OK"
404: 未找到页面 
……
 - 由于时间的关系就只能写个很简陋的小页面来显示一下AJAX的强大之处——异步请求，异步请求的好处就在于不更新页面就能获取数据，并且能够根据用户的想法来决定是否加载数据，链接就在下面。代码就不贴了，都是很基本的代码，没啥值得贴出来的。

  三、感言
--------------------
-  AJAX能用的地方还有许多，最近也只是浅浅地去了解了一下，它在服务器端还有很多的作用，想要弄明白服务器端的作用估计也得等我学些Node.js才能搞得懂了吧~~（走上全栈的不归路）~~。唉，真是希望时间能够多一些，好让我多学点东西。

下面是链接：
戳我→  http://fe1yyx.coding.me/SmallThings/ajax.html
p.s.别用360浏览器等~~辣鸡国产~~浏览器，用Chrome谷歌或者Firefox火狐效果最佳。
