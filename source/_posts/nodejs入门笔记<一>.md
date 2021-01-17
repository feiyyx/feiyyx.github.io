---
title: nodejs入门笔记<一>
tags: 
      - NodeJS

---

nodejs入门笔记<一>
=================================

〇、前言
---------------------------

- 记录一下在公司内网学习课程的所记的笔记。结合网络上一些比较好的文章来看。<!--more-->

- ~~所有东西都待自己去敲一遍，但是到底要什么时候才有时间自己敲呢（咕咕咕）~~

一、笔记
-----------------------------

- 知道了NodeJS读取模块时使用的是CommonJS。关于CommonJS与前端模块化的相关资料：[前端模块化CommonJS,AMD,CMD,ES6][1]

- 了解到npm version的用处以及各个位置(major, minor, patch)的区分，并且懂得了~、^符号是如何工作的：

  - ~： ~1.0.1 => 匹配大于等于1.0.1的patch版本(例如1.0.1、1.0.2、1.0.3 ...不匹配x.x.x和1.x.x)

  - ^： ~1.0.1 => 匹配大于等于1.0.1的patch和minor版本（例：1.0.1、1.0.2、1.1.1...不匹配x.x.x）

- 了解了Promise是如何诞生的（为了取代迭代器，解决回调地狱）。并且由于其在EventLoop的位置，Promise会在主进程结束后才进行第一轮的触发。

  - 异步解决方案：流程库控制、尾触发和next(中间件)，step(队列)
  
  - 学习资料：
  	- [promise到底是在resolve时被推入微任务队列还是在then的时候呢？][2]
  	- [这一次，彻底弄懂 Promise 原理][3]

- 中间件(BFF - Backend For Front)
	- 学习资料（比较少）：
	- [Node.js 中间件模式][4]
	- [中间件（middleware）][5]
- 优点：

  - 前后端彻底分离，即使是后期有微服务迁移，也不需要改动前端代码

  - 业务更向前靠拢，琐碎的api由前端开发自己决定，更适配前端框架

  - BFF可以自开mock，插件也能生成API文档（补充：还可以自主进行接口的单元测试）

  - 留给后端更清晰的服务边界，只需要提供粗粒度的接口即可

- 缺点：

  - 请求延迟增加

  - 前端开发人员开发成本、测试成本、运维成本增加

  - 工程化：本地开发(dev-server/mock/hot-reload/live-reload)、编译(babel/scss/pug/Vue/多端同构)、打包(代码压缩/分快)、测试(unit test)、工作流(grunt/npm scripts/ci/gulp)、工具(cli，可视化工具)

- 如何自己实现一个cli：
	- 学习资料：[🎉用Node.js开发一个Command Line Interface (CLI)][6] 

  - 声明可执行文件

  - 接收若干个选项参数（参数处理）【用process.argv获取参数，用commander处理(链式调用)】

  - 实现对应的功能

二、结语
------

- 不能再这么浪费平时的时间了，还是要好好学习。引入新的TODO模式给自己鞭策一下。

- 下周TODO: 
  - [ ] 完成一次自定义cli《Hello World》
  - [ ] 输出SQL课程<二>笔记


上传于2021-01-17

[1]: https://juejin.cn/post/6844903576309858318
[2]: https://blog.csdn.net/XuM222222/article/details/88582131
[3]: https://juejin.cn/post/6844904063570542599#heading-11
[4]: https://juejin.cn/post/6844903619209199624
[5]: https://juejin.cn/post/6844903602511675405
[6]: https://zhuanlan.zhihu.com/p/38730825