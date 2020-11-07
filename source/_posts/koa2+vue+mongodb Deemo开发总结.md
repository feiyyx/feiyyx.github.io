---
title: koa2+vue+mongodb Deemo开发总结
tags: 
      - 前端
      - JavaScript
      - Vue
      - Koa2
      - 后端
---

koa2+vue+mongodb Deemo开发总结
=================================

一、前言
-------------------------

- 为了给自己练手，学习新姿势，总结并且体会学到的知识在项目�中的食用方法，于是我趁着假期在家里写了一个前后端结合的Deemo，链接如下：<!--more-->

- https://github.com/feiyyx/vue-koa2-ItemSearch/

- 以前还是个网瘾少年的时候总想写一个可以查询+许愿的DNF装备查询的应用，这个坑其实很早很早以前就挖了，但是大三下真的太忙了，只有在暑假才能好好的写一写

- 写的时候不由得感慨时光过得真快，一转眼DNF已经弃坑了，身边的人也不怎么玩了，可能把这个坑填完也算是追忆一下自己的青春吧hhhhhh

- 由于是本菜鸟的练手作品，所以代码难免写得稀烂，以及还有好多可以优化的地方没有去优化，代码和命名也不那么规范，希望各位走过路过的大佬们多提宝贵意见Orz

二、技术栈
--------------------------

- vue2 + vuex + vue-router + webpack + koa2 + koa-body + koa-session-minimal + mongodb

- 使用了vue-cli和koa-generation

三、项目运行
--------------------------

- 克隆到本地

> git clone https://github.com/bailicangdu/vue2-elm.git  

- 前端运行：

``` bash
cd frontDeemo/

npm install 或 yarn(推荐)

#http://localhost:8080/
npm run serve
```

- 后端运行：

``` bash
cd serverDeemo/

npm install 或 yarn(推荐)

#http://localhost:3000/
npm start
```

- 数据库（MongoDB）

- 使用了Robo3T，连接地址：
> http://localhost:27017/deemo

- 具体键值对形式写在serverDeemo内

``` bash
|- deemo // Database名
    |- sessions  //存储sessions
    |- test      //其实是装备的Collections
    |- users     //存储账号信息
```

四、目标
--------------------------

- [x] 模糊查询生成suggest列表 -- 完成
- [x] 显示查询装备的完整信息 -- 完成
- [x] 管理员/普通用户登陆 -- 完成
- [x] 使用cookie+session记录登录状态 -- 完成
- [x] 管理员向服务器上传新装备信息(包括图片) -- 完成
- [x] 装备许愿选定/取消 -- 完成
- [x] 装备许愿数据上传、保存游客许愿数据(LocalStorage) -- 完成

五、总结
------------------------

- 经历了一个月的“开荒”，这个deemo终于完结撒花了！！其中自己写了一些常用的工具，像是用xhr封装了post和get方法等。唉，以后有机会一定看看axios和lodash的源码，学习造轮子的大师傅的思路。

- 写deemo的过程中，许多东西真的是两眼一抹黑，无论是书写命名规范，又或者是判断是否可以分离成组件的标准，还有许许多多的坑，一个人摸着石头过河真的太难受了，不过好在轮子够多，对着google和stack overflow查找问题也还基本上都能解决。但是还是想有个人能够领着自己进门鸭……

- 马上也要开始秋招了，接下来也要边写另一个deemo，边准备面试和笔试的技术细节，希望这个秋天能是我的丰收之秋吧……

![006m97Kgly1g09pw6ghh5j305b04cq3u.jpg-42.8kB][1]

上传于2019-07-31


  


  [1]: http://static.zybuluo.com/feiyyx/g9mf9eo85lprm9v5qpnh9yec/006m97Kgly1g09pw6ghh5j305b04cq3u.jpg