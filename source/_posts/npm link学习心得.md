---
title: npm link学习心得
tags: 
      - npm
      - NodeJS
---

npm link学习心得
=================================

〇、前言
---------------------------

- npm 是一个神奇的包管理器，随着任务与开发的深入，我也逐渐了解了一些npm的神奇小技巧，这一次就是学习了一下npm link的用法。<!--more-->

- npm link本质上是为了节省我们程序员的时间而内置在npm内部的软连接指令，因为软连接在不同操作系统下的操作指令不同，所以npm官方就整了一个指令来方便我们开发者对未来要发布的npm包进行操作/测试。

一、动工
-----------------------------

- 为了效果，首先准备一个用于测试发布的npm包`/project/test1`，然后主程序`/prioject/main`，package.json内的name也要和npm包一致（强调），首先我们先用cd指令cd到测试npm包中，并使用npm link指令：
> cd project/test1 && npm link

然后我们就能看到一个进度条在加载，加载完后应该能看到这个样子：

<center>![0.png](https://i.loli.net/2020/10/25/rlDwRj6VGIgFyYZ.png)</center>

- 这就表明你的这个npm包已经软连接完毕，可以在npm的全局模块中查看，这个全局模块在npm的安装路径上，我们可用`npm config get prefix`查看npm的安装路径，一般来说如果都是默认安装的话全局模块Win10的`在C:/User/yourname/AppData/Roaming/npm/node_modules`，Mac的在`/usr/local/lib/node_modules`，Linux的我猜和Mac差不多（逃）。以下是我电脑里的截图：

<center>![1.png](https://i.loli.net/2020/10/25/E9nHFhyeqzNRc1b.png)</center>

可以看到有一个软连接符号在文件夹上，点进去也可以跳转到`/project/test1`。

- 之后就简单了，再使用cd指令跳转到然后link一下，不过这一次要指名道姓：
> cd project/main && npm link test1

- 然后就能看到main项目的node_modules文件夹中有一个test1的软连接了。

<center>![2.png](https://i.loli.net/2020/10/25/bdPtX2Jhfr6soKw.png)</center>

- 你以为到这里就结束了吗，太天真了，虽然可能细心的小伙伴在下面环节不会出错，但是我还是要把我的浪费时间血泪史贴出来，让像我一样粗心的小伙伴免遭此劫。

### 私有包link方法

- 私有包的包名会有一个@前缀（表示其作用域），并和包的实际名称以左斜杠`/`分开，类似于这样`@test/test1`，然后我们这次要折腾`project/test1`和`project/test2`两个npm包，主项目还是使用`project/main`，test1包命名(package.json内的name)为`@test/test1`，同理可得test2包的`@test/test2`。

<center>![3.png](https://i.loli.net/2020/10/25/9w3KoF2pa4mIvY5.png)</center>

- 还是故技重施使用`cd xxx && npm link`大法获得test1与test2的全局软连接模块，不过这次我们可以看到目录是这样的：

<center>![3.5.png](https://i.loli.net/2020/10/25/tsdoPY2Wg76pTwH.png)</center>

npm生成包的时候若是为私有包，则会在包外面再套一层文件夹，包的文件夹结构也就是和命名一致`../node_modules/@private_name/package_name`。

- 这里就到了注意点了！在这个时候我们必须得按照全名来link我们想要进行link的npm包，不是test1或者test2，是`@test/test1`或者`@test/test2`，这个全名为package.json内设定的name，之后输入
> cd project/main && npm link @test/test* //*号代表1或2

链接好后同样也可以在main的node_modules下看见和全局模块里一样的结构。

<center>![4.png](https://i.loli.net/2020/10/25/NOS9G4PUB32hgqz.png)</center>

二、结语
-----------------------------

- 我是真的没想到我能在这么小的地方折腾半个多小时，看来还是太年轻Orz，没有遭受过webpack+npm的混合双打。未来肯定会有很多机会的XD

- P.S.这篇文章一个礼拜多之前就写好了，但是当时懒癌发作不想上图，所以拖到了现在（逃）

参考：

- [https://medium.com/@EtrexKuo/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8-npm-link-%E9%80%B2%E8%A1%8C-node-module-%E6%B8%AC%E8%A9%A6-9d3b98db768c](https://medium.com/@EtrexKuo/如何使用-npm-link-進行-node-module-測試-9d3b98db768c)

- https://mobilesite.github.io/2017/03/12/npm-link-local-module/

上传于2020-10-25