---
title: 初探Koa2（一）
tags: 
      - Koa2
      - 后端
      - NodeJS
---

初探Koa2（一）
=================================

一、前言
--------------------------
 - 最近一段时间除了各种姿势投简历，其实还有在探索koa2作为中间件和后端的食用方法。因为想在暑假的时候搭一个在服务器上的博客，所以在根据网上各式各样教程来搭建之前，先探索一下工具的使用方式，熟悉一下他们的指令。虽然每次都有看源码的欲望，但是事实上学习新的东西还来不及，暂时还没有时间停下来好好巩固自己，只有边学变巩固了。<!--more-->

 - 虽然这篇文章的标题是初探koa2，但是实际上已经是在我帮女票写完他们组的大作业之后写得了，加上探索和使用已经过了半个月了，那么废话少说进入正题
   
二、搭建环境
--------------------

### 1、 koa-generator

 - 为了少跳一些坑，所以我决定找一个现成的koa脚手架来搭建deemo项目，这个时候就找到了koa-generator[官方文档][1]，下载代码如下：

 > npm install -g koa-generator

 - 这个koa脚手架还能选择使用koa1和koa2，为了紧跟潮流，我们当然选择使用koa2啦。输入代码：

 > koa2 deemo

 - 指令的第二个参数是filename，输入指令后可以在cmd中见到如下提示：
 
![1.png-117.2kB][2]

 - 所以说这个脚手架只是帮你把所需要的目录和包名给好了，我们还是得cd进这个文件夹来npm/yarn install下载包。这个过程就省略了。

- install完之后，按照前面给的命令启动，然后发现emmmmm好像啥事都木有发生。

![2.png-10.6kB][3]

- 然后我去看了下这个脚手架的文档，发现小伙汁并没有告诉我们这玩意开在哪个端口，于是我只有顺着它的启动指令去bin/www文件里查看。发现这样的代码：

![3.png-19.5kB][4]

- 这个脚手架有点意思，虽然我之前直接使用app.listen(8000)来强行多监听一个8000端口，但是这样似乎是会使得3000和8000端口都被占用，所以如果有需要就直接去这里改吧。

- 之后去 **localhost:3000** 就能看到运行起来的服务器啦。

![4.png-44.8kB][5]

 
### 2、 koa2语法

- 由于自带了koa-router，所以其的逻辑应该基本上都在/routes文件夹里，我们打开其中的index.js一探究竟：

![5.png-100.5kB][6]

- 可以看到，koa2的语法是基于JavaScript ES8拓展的[async][7]和[await][8]，具体点上面的链接就行了，至于具体使用，可以粗略地把他们都看做是Promise对象，await则是只能在aysnc中用的等待Promise对象，整个aysnc函数要等到await全部运行完毕才会返回值。

- 掌握好这两个方法的特性我们就可以愉快的食用Koa2了……才怪，Koa2也有其他的语法例如**await next()**,以及"私人定做上下文"的**ctx.[各种东西]**也都要自己一步一步学习。不过由于中间件已经被配好了，而且我们接下来要做的东西还很基础，用不上高级的设置，所以我们可以暂时不用管**await next()**和**ctx.xxx**，直接学习和修改routes/index.js文件里的代码就好了。

- 这样的话写起来的koa2没那么原生的了，不过借助koa-router工具我们能更好的体会koa2的强大和易用。具体使用留在下一篇，这里先偷个懒
   
三、总结
----------------------------------------  

- 如果借助脚手架的话，是能最快感受到koa2的魅力的，不过这样也会少很多掉坑里警惕自己的机会。不过我学习的方法就是先上手再看基础，不从一定高度发现登山的乐趣，会先登山的过多准备被消磨了耐心和冲劲。不过我还是先在这里定一个小目标——暑假的时候自己写一写原生的koa2，并且看一看它短小精悍的源码hhhhh




上传于 2019-4.14



  


  [1]: https://github.com/17koa/koa-generator
  [2]: http://static.zybuluo.com/feiyyx/6wnagsoqhg86oft3n11qt5lp/1.png
  [3]: http://static.zybuluo.com/feiyyx/4j7am6l49x1ec8rgh6b6txay/2.png
  [4]: http://static.zybuluo.com/feiyyx/5gp2f2k4bcxdq9vkzp78428b/3.png
  [5]: http://static.zybuluo.com/feiyyx/921poukrhcd017rybeu0tore/4.png
  [6]: http://static.zybuluo.com/feiyyx/3ilbm3r07t71ltgovzq9bt4n/5.png
  [7]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function
  [8]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/await