---
title: 初探Koa2（二）
tags: 
      - Koa2
      - 后端
      - NodeJS
---

初探Koa2（二）
=================================

一、前言
--------------------------
 - 接着上篇之后，又继续对koa2进行深入研究。<!--more-->

   
二、搭建后端
--------------------

### 1、 koa-router

 - 正常的koa2是只有一个主页的，若是想丰富api得使用之前所说的koa-router路由插件。我们打开routes/index.js，然后根据里面所写代码进入/string其效果如下：

![1.png-148.9kB][1]

- 我们可以看到进入这个页面相当于使用了get来请求这个页面的response，这些response是可以变为我们前端经常接触的JSON数据的。只需要修改一下代码：

![2.png-100.2kB][2]
 
- 这样我们就能直接创造出接口了，不过前边试了这么多都是get请求，那么有没有post请求呢？本着依葫芦画瓢的想法，我尝试着把router.get修改为了router.post，然后使用postman来请求数据：

![3.png-90.4kB][3]

- P.S.本来原生的Koa2是无法处理post过来的数据的，但是将post过来的数据经过处理成get过来的数据样式后就可以了。笔者在这里选择了koa-bodyparser来作为中间件来处理post过来的数据。

### 2、 跨域问题与koa-cors

- 提到api的时候也就不得不提到我们常说的跨域问题：**域名、端口和协议三者只要有一者不一样都是跨域，**这个时候就要设置CORS头来允许跨域

- 有两种办法，第一种是在中间件里加上这样几行关于CORS头的代码：
```
app.use(async (ctx, next) => {
    //表明服务器支持跨域的网站为http://localhost:8080
    //若为'*'表示支持所有网站跨域
    ctx.set('Access-Control-Allow-Origin', 'http://localhost:8080');
    //表明服务器支持发送方带有Cookie
    ctx.set('Access-Control-Allow-Credentials', true);
    //表明服务器支持的头信息字段.
    ctx.set('Access-Control-Allow-Headers', 'X-Requested-With, Accept, Origin, Content-type');
    //表示具体请求中的数据类型信息
    ctx.set('Content-Type', 'application/json');
    //表明服务器支持的请求方法
    ctx.set('Access-Control-Allow-Methods', 'PUT,DELETE,POST,GET,OPTIONS');
    // 当请求方法是PUT或DELETE等特殊方法或者Content-Type字段的类型是application/json时，服务器会提前发送一次请求进行验证
    // 下面的的设置只本次验证的有效时间，即在该时间段内服务端可以不用进行验证，单位为秒
    ctx.set('Access-Control-Max-Age', 600);
     /*
        CORS请求时，XMLHttpRequest对象的getResponseHeader()方法只能拿到6个基本字段：
            Cache-Control、
            Content-Language、
            Content-Type、
            Expires、
            Last-Modified、
            Pragma。
    */
    // 需要获取其他字段时，使用Access-Control-Expose-Headers，
    // getResponseHeader('{你需要返回的头}')可以返回我们所需的值
    ctx.set("Access-Control-Expose-Headers", 'WWW-Authenticate ，Server-Authorization');

    await next()
}
```

- 第二种方法是使用npm下载koa-cors，然后使用require引入：

> const cors = require('@koa/cors');

- 然后使用app.use()挂载到服务器上：

```
app.use(cors({
    origin: 'http://localhost:8080'
    credentials: true,
    maxAge: 600,
    allowMethods: ['PUT', 'DELETE', 'GET', 'POST', 'OPTIONS'],
    allowHeaders: ['Content-Type', 'X-Requested-With', 'Accept', 'Origin'],
    exposeHeaders: ['WWW-Authenticate', 'Server-Authorization']
}));
```

- P.S.S在前端自己写ajax的时候，也要记得同步将XHR.withCredentials设置为true,不然也会无法将Cookie发送至后端服务器处。

> ... XHR.withCredentials = true; ...

- 阮一峰大佬的这篇关于CORS的[博文][4]讲得比较全面，因为这篇文章的主题是koa2的使用，所以我这里只能粗略的介绍一些，想要具体了解CORS的运作还是去看看他或者其他人讲解的文章，我就不一一细说了。

### 3、 Koa2 RESTful化思路

- 一般后端返回给我们的api都是 **xxx.com/api/:someAPI** 类似于这种样子的，我们自己在koa2中玩耍的api其实也很容易弄成这样，只需要这样子修改即可：

![4.png-66.2kB][5]

- 但是这样会显得代码很冗长而且很比较凌乱，所以这个时候我们要用到的就是REST——Representational State Transfer（表现层状态转移）。我们使用router.use以及另起一个route：

```
const router = require('koa-router')()
const api = require('koa-router')()
...
api.get('/string', async (ctx, next) => {
  ctx.body = {
    test: 'test data'
  }
})
```

- 之后使用router.use挂载至/api上

> router.use('/api', api.routes(), api.allowedMethods());

- 然后重启服务器，就可以发现我们将/string放入/api/..里面啦

![5.png-139.4kB][6]

- restful这个东西我也是才接触，感觉知乎上的基础讲解的比较好，阮一峰大大讲得比较浅显易懂但是似乎不够严谨，两者结合起来看比较好，当然如果能把rest的论文看下去就更厉害啦。还有一篇具体应用的讲解的也比较好，链接我就贴在下面了。
 - [知乎提问][7]
 - [阮一峰博客][8]
 - [Koa2应用][9]

三、总结
----------------------------------------  

- 在深入了解koa2的时候又学到了很多其他的东西，知识就像一棵树一样，顺着枝干又有很多分叉。学着学着就开始顺着各个枝干走下去了，真的是学无止境呀~

- 学完koa2基础的东西之后，可以向着它内部好好探索一番，这里有一个大佬归纳总结了一下koa2的基础和进阶，我看了觉得写得很棒，所以放在这里和大家分享一下：
 - [《koa2进阶学习笔记》][10] 

- 每每使用过别人写好的包之后，总是不由得感慨为什么这些包的作者老哥们这么厉害，然后每次都想看看他们包里写的代码长啥样。但是身为一个前端最重要的事情是完成业务逻辑，所以只能按捺住内心的冲动，然后好好磨练业务能力。希望有朝一日我也能为开源社区做些贡献吧。


上传于 2019-4.15



  


  [1]: http://static.zybuluo.com/feiyyx/s1ctoyosueodzkcpxknokf07/1.png
  [2]: http://static.zybuluo.com/feiyyx/40d5vifutwubsshfwtdhaiqt/2.png
  [3]: http://static.zybuluo.com/feiyyx/bo5qcwouhajza0qc3999dr07/3.png
  [4]: http://www.ruanyifeng.com/blog/2016/04/cors.html
  [5]: http://static.zybuluo.com/feiyyx/z2i6q40ooorwsmgbbdb8ierl/4.png
  [6]: http://static.zybuluo.com/feiyyx/vd2otc3wef9j1e3hk951on0n/5.png
  [7]: https://www.zhihu.com/question/28557115
  [8]: http://www.ruanyifeng.com/blog/2018/10/restful-api-best-practices.html
  [9]: https://www.jianshu.com/p/f59594b90500
  [10]: https://chenshenhai.github.io/koa2-note/