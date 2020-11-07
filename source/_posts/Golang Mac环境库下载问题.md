---
title: Golang Mac环境库下载问题
tags: 
      - Golang
---

Golang Mac环境库下载问题
=================================

〇、前言
--------------

- 最近在和朋友折腾Golang的安装配置和学习，于是有了这篇超级无敌短的记录+吐槽文。有一说一，这个Golang的配置比Java还难麻烦啊！！<!--more-->

<center>![9867a9dffbf10518.png](https://i.loli.net/2019/12/11/ZPDARQXLzsqlhNE.jpg)</center>


一、问题整理+解决
-------------------------

- 安装的时候没有太多问题，Mac环境下使用`brew`输入

>   brew install go

- 就可以了，这个倒是简单的很，一会就安装好了，这里我装的是`1.13.4`版本的golang。本着一颗极客的心，我们想的是能自己解决的事情就自己解决，于是乎打开vscode和科学上网工具，按照vscode给的提示来下载go开发所需的库。

- 这个时候问题来了，下着下着，你就会发现这么多的FAILED：

<center>![2.jpg](https://i.loli.net/2019/12/11/yKdZEPwV2YX1h7I.jpg)</center>

- 本着不行我们多试几次的精神，我把库全删了，然后重新进入vscode让他下载库文件，结果还是大部分都是FAILED。What？Why？

<center>![3.jpeg](https://i.loli.net/2019/12/11/lRvSbET8CGOWozF.jpg)</center>



- 然后上网搜了一下，试着从github直接将库文件下载下来然后在下载下来的文件夹内使用`go install`，发现有一些安装成功了，还有一些怎么安装都是显示如下英文，怎么弄都没用，去谷歌查询也没有很明确的结果：

<center>![5.jpg](https://i.loli.net/2019/12/11/c52Uh3Ct6ISdrZA.jpg)</center>

- 到了这一步我和朋友就懵逼了，这些都是啥玩意？？？然后我发现我看到的[教程][1]里有这一句话：
    - *当下载完成后，你会发现`%GOPATH%\src\github.com\golang`多了一个tools目录*

- emmm这句话就让我想要尝试了一番，于是我就在那个目录下新建了一个golang目录。然后我又重复上面的操作：将所有库文件删除，然后进入vscode重新安装，结果奇迹发生了：

<center>![6.png](https://i.loli.net/2019/12/11/k9rla8MohznewZF.jpg)
</center>

- 我的表情是这样的：

<center>![6.jpg](https://i.loli.net/2019/12/11/wTmgBVYyXxMdhDv.jpg)</center>

- emmmm总而言之看上去是成功了，打开`bin`目录查看库文件也一个都不少，应该就是安装成功了吧。

二、结语
-------------------------

- 希望这篇文章能够为想在vscode进行Golang开发的小伙伴们提供一个解决问题的方法。

- 题外话：但是在之后的vscode实际使用起来总是感觉差点什么，于是我决定使用goland来进行Golang的开发，然后我和我的朋友就出现了以下的对话：

<center>![7.png](https://i.loli.net/2019/12/11/Hh5JezgDWEoun4B.png)</center>

- ~~果然还是一个集成好的开发环境更令人舒心啊XD，goland🐂🍺~~。

<center>![8.png](https://i.loli.net/2019/12/11/YpvBzsZoIFjPRnk.png)</center>

<br>
上传于2019-12-11


  [1]: https://cloud.tencent.com/developer/article/1013066
  [2]: https://jkchao.github.io/typescript-book-chinese/#why