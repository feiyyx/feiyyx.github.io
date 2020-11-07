---
title: 原生JS前端路由原理学习笔记
tags: 
      - JavaScript
      - 前端
---

原生JS前端路由原理学习笔记
=================================

一、前言
--------------------------
 - 在看关于vue和react相关知识的时候，我突然对他们单页路由router的实现有了很浓厚的兴趣。于是自己查找资料并且动手实现了一下，虽然很简陋，只有最最最最最基础的功能（path+HTMLcomponent），不过还是觉得值得拿出来一写，毕竟自己也思考过了hhh<!--more-->

二、Router
--------------------

### 1、 Router

 - 那么前端中什么是Router呢？从Egg.js中我们可以得知前端中路由 **主要用来描述请求 URL 和具体承担执行动作的 Controller 的对应关系，并且自己内部有一系列的规则。**但是有个很重要的一点：前端路由进行的请求不会刷新页面！

 - 由于根据传入路由的config来进行一系列的请求和渲染操作，所以我猜因为这一点和路由器对着自己的路由表进行寻址较为类似，所以称之为前端路由。

 - 这个技术的发展得益于AJAX这一方法的出现，它不仅可以发送取回消息的时候不重载刷新页面，并且由于这一点其能够使得网站更快回应用户的操作，所以可以给我们带来很多其他的异步交流体验，其中之一就是前端路由带来的SPA（单页应用）。废话不多说，Show the codes！
 
### 2、Hash路由

 - 前端中的Hash呢就是我们经常可以在网页中见到的"#"号+字符串，本来的用法是与`<a>`标签结合，成为页面上的锚点。但是随着hashchange的其他用途被开发，以及它自身的特性（点击后压入历史栈），Hash也就成为了可以实现前端路由的一种方式。

 - 首先我们先要建立一个类，然后在内部存储传入的config数据，并且确认我们要实现的功能，由于实现的功能比较简单，所以定义一个init()一个routeChange()就足够了：

```JavaScript
class Router {
    constructor(params = { routes:[] } ) {
		this.router = params.routes
    }
    init() {}
    routeChange(){}
}
```

 - 然后我们先确定我们要实现什么功能，我们要实现的功能是将component里的内容渲染进页面，这里就怎么简单怎么来：

```JavaScript
routeChange() {
    console.log("Hash Changed!");
    let nowHash = window.location.hash;
    let _index = this.router.findIndex((item, index) => {
        return `#${item.path}` === window.location.hash;
    })
      console.log(_index);
    if(_index >= 0 ) {
        document.querySelector("#router").innerHTML = this.router[_index].component;
    } else {
        let defaultIndex = this.router.findIndex((item, index) => {
            return item.path === "*"
        });
        if(defaultIndex >= 0 ) {
            window.location.hash = this.router[defaultIndex].redirect
            document.querySelector("#router").innerHTML = "index";
        } 
      }
    }
```
 - 我们先获取改变后的`nowHash`，使用`findIndex`方法寻找config中的是否存在这个路径`path`，若存在则渲染html在id为router的标签中；若不存在则重定位回index。````
 
 - 之后我们再将其挂载至`hashchange`事件监听上，写入init()中：
 
```JavaScript
init() {
	 window.addEventListener("hashchange", this.routeChange.bind(this), false);
}
```

- 这样一个极其简陋的Hash路由就完成了hhhh，效果如下：

<iframe height="265" style="width: 100%;" scrolling="no" title="Hash Router" src="//codepen.io/feiyyx/embed/LYPzVMp/?height=265&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/feiyyx/pen/LYPzVMp/'>Hash Router</a> by feiyyx
  (<a href='https://codepen.io/feiyyx'>@feiyyx</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 3、 History路由

- 前端路由最重要的地方是什么？我认为是点击`<a>`标签后url压入历史栈，并且不刷新页面，好在H5中新增了`pushState`和`replaceState`方法以及`popstate`事件，关于事件的介绍就不用我多说了，复制一下MDN：

  - `pushState`：使用history.pushState()可以改变referrer，它在用户发送 XMLHttpRequest请求时在HTTP头部使用。简单的意思就是改变url的同时将url压入历史栈也不刷新页面。同时它也有三个参数pushState(state, title, url);
  
  - `replaceState`：这个方法的使用形式和pushState十分类似，不同的地方在于它是替换掉位于历史栈栈顶的元素而不是压入历史栈中。
  
  - `popstate`：当活动历史记录条目更改时，将触发popstate事件。如果被激活的历史记录条目是通过对history.pushState（）的调用创建的，或者受到对history.replaceState（）的调用的影响，popstate事件的state属性包含历史条目的状态对象的副本。
参照
  - **需要注意的是调用history.pushState()或history.replaceState()不会触发popstate事件。只有在做出浏览器动作时，才会触发该事件，如用户点击浏览器的回退按钮（或者在Javascript代码中调用history.back()）**
  
  - 更具体的说明请点击右边的MDN链接：https://developer.mozilla.org/zh-CN/docs/Web/API/History_API

- 有了上面的知识后，我们就可以用更优雅的方式实现前端路由啦～实现形式与Hash Route差不多，直接上代码：

```JavaScript
class HistoryRouter {
    constructor(params = { routes:[] } ) {
		this.router = params.routes
		this.indexHTML = document.querySelector("#router").innerHTML;
    }

    init() {
		console.log("H5Router Initial!");
		let _this = this;
        document.querySelectorAll(".route").forEach((item, index) => {
			item.addEventListener("click", (e = window.event) => {
                let _href = e.target.getAttribute('href');
				e.preventDefault();
				window.history.pushState({ path: _href}, '', _href);
				_this.routeChange();
			}, false) 
		})
		_this.routeChange.bind(_this), false);
		window.addEventListener("popstate", _this.routeChange.bind(_this), false);
	}
	routeChange() {
		console.log("History Changed!");
		let nowHref = window.location.href,
			tempReg = null;
		console.log(nowHref);
		let _index = this.router.findIndex((item, index) => {
			tempReg = RegExp(item.path);
			return nowHref.search(tempReg) > 0 ;
		})
		if(_index >= 0 ) {
			document.querySelector("#router").innerHTML = this.router[_index].component;
		} else {
			let defaultIndex = this.router.findIndex((item, index) => {
				return item.path === "*"
			});
			if(defaultIndex >= 0 ) {
				document.querySelector("#router").innerHTML = this.indexHTML;
			} 
		}
	}

}

let router = new HistoryRouter({
            routes: [{
                path: '/home',
                component: "<h1>主页</h1><a href='http://feiyyx.cc'>http://feiyyx.cc</a>"
            }, {
                path: '/github',
                component: "<h1>Github</h1><a href='https://github.com/feiyyx/'>https://github.com/feiyyx/</a>"
            }, {
                path: '/pixiv',
                component: '<h1>Pixiv</h1><a href="https://www.pixiv.net/member.php?id=6999089">https://www.pixiv.net/member.php?id=6999089</a>'
            }, {
                path: '/about',
                component: '<h1>关于</h1><h3>从零开始的前端生活，以及朝着大触之路慢慢爬行的小辣鸡_(:3 」∠)_。</h3>'
            }, {
                path: '*',
                redirect: '/index'
            }]
        });

router.init();

```

- 代码与Hash Router大同小异，只是init()中增加了对`<a>`标签的事件监听，使用了`event.preventDefault()`来阻止对`<a>`标签本身的事件，并且挂载我们想要发生的事件。

- 由于的性质，所以我们必须在点击标签后就进行组件的渲染，而不是监听事件的发生。但是你要是问我可不可以有偷懒的方法，那我告诉你还是有的hhhh，查阅资料的过程发现了一种神奇的方法——对pushState和replaceState进行事件的注册，之后进行监听：

```JavaScript
/*实现H5Router的第二种方法
* 注册pushState和replaceState事件
*/
(function (global) {
  var signupEvent = function(name){
    var orig = history[name];
    if(!orig) return new Error("No History Attribute!");
      return function() {
        let cb = orig.apply(this, arguments);
        let e = new Event(name);
        window.dispatchEvent(e);
        return cb;
    }
}

window.history.pushState = signupEvent('pushState');
window.history.replaceState = signupEvent('replaceState');
})(window);
```

- 第二种方法和第一种集成在一起，都在codepen里：

<iframe height="265" style="width: 100%;" scrolling="no" title="HTML5 Router" src="//codepen.io/feiyyx/embed/VwZMaZo/?height=265&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/feiyyx/pen/VwZMaZo/'>HTML5 Router</a> by feiyyx
  (<a href='https://codepen.io/feiyyx'>@feiyyx</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

三、总结
----------------------------------------  

- Router的实现涉及到不少浏览器原生事件的使用，通过初步对前端路由实现的探索与学习，感觉自己对这些实现前端路由所需要用到的原生api的调用和它们内部的原理有了一定的了解。未来也有计划对Vue-Router和React-Router的源码进行深入学习，这次对hash和history的前端的实现也算是为这个未来的目标打下一点基础，不至于连一些原理性的东西都康不懂hhhhh

- 参考资料
  - https://juejin.im/post/5c5014ef51882525aa50a25b

  - https://zrysmt.github.io/2017/04/28/%E9%98%BF%E9%87%8C%E5%B7%B4%E5%B7%B4%E6%A0%A1%E6%8B%9B2017%E5%89%8D%E7%AB%AF%E7%AC%94%E8%AF%95%E9%A2%98%E7%9B%AE-%E5%8E%9F%E7%94%9Fjs-html5-%E5%AE%9E%E7%8E%B0%E4%B8%80%E4%B8%AA%E8%B7%AF%E7%94%B1/
 
  - https://cloud.tencent.com/developer/article/1394826

  - https://codepen.io/xiaomuzhu/pen/QmJorQ/


上传于 2019-09-02



  
