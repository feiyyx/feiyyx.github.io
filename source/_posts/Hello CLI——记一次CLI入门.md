---
title: Hello CLI——记一次CLI入门
tags: 
      - NodeJS

---

Hello CLI——记一次CLI入门
=================================

〇、前言
---------------------------

- CLI，也就是命令行界面 (Command-line interface，)是在图形用户界面得到普及之前使用最为广泛的用户界面，它通常不支持鼠标，用户通过键盘输入指令，计算机接收到指令后，予以执行。也有人称之为字符用户界面。—摘自[维基百科][1] <!--more-->

- 我们平时使用的vue-cli、angular-cli等框架脚手架，以及webpack等工具，都是集成了开发者们经常使用的指令，或者是经常使用的操作，甚至还有一些重复性的机械操作也可以由cli完成。如此一来，开发者就可以省下时间，提升工作效率。

- 废话少说，let's show code～
<center>![0.jpeg](https://i.loli.net/2021/01/24/hoVF1PrSMsglaBq.jpg)</center>

一、动工
-----------------------------

### 1、初始化npm模块并添加bin文件

- 首先我们输入如下指令生成所有使用npm开发的模块的基石——package.json：

> npm init -y

<center>![1.png](https://i.loli.net/2021/01/24/UkMBYEc64X8R29r.png)</center>

- 然后我们在该目录下新建一个bin文件夹（其用来指定各个内部命令对应的可执行文件的位置）并且同时新建一个index.js文件作为`hello`指令的入口。

<center>![2.png](https://i.loli.net/2021/01/24/oW4rpsgTCQDZwfl.png)</center>

- 在`./bin/index.js`文件的最上面加上如下代码告诉系统使用node运行该文件：
> #!/usr/bin/env node

- 如果没有加上的话会报错：

<center>![4.png](https://i.loli.net/2021/01/24/1ylwK7JHVdtm6SY.png)</center>

### 2、使用命令进行全局安装

- 之后执行
> npm install -g

- 将hello-cli安装到全局。当出现类似下图所示文字的时候，一个最简单的cli诞生了。

<center>![5.png](https://i.loli.net/2021/01/24/3N2fA1ZVyFez6Co.png)</center>

- 之后任意找个位置输入hello 命令，就执行了你的 ./bin/index.js文件，并console.log出了“Hello CLI”。

<center>![6.png](https://i.loli.net/2021/01/24/V5IsP6lJBqW8SNc.png)</center>

- 到此，全文完……才怪咧，到了这一步只能说我们搭好了一个cli的骨架，结构内容甚至还没有进行填充，下面继续进行完善CLI的结构。


### 3、为cli添加command模块并更改入口文件的代码

- 这一步的核心模块是commander模块，它的作用是可以识别你写入cli的命令参数并回调执行一系列方法。首先使用下面这条语句下载这个模块：
> npm install commander -\-save

- 然后我们新建一个packages，并在它的目录下新建command文件夹，最后为此添加一个文件`init.js`。

```javascript
// init.js
const init = () => {
    console.log('Init Hello CLI!!');
    console.log('Something happened...');
};

module.exports = init;
```

- 这一步完成后还不能立马进行命令的使用，我们还需要改造入口文件：

```javascript
// bin/index.js
#!/usr/bin/env node
const program = require('commander');
const path = require('path');
const fs = require('fs');
const init = require('../packages/command/init');

const configFile = '../packages/hello/config/hello.config.js'

// 获取config参数
let config = {};
if(fs.existsSync(path.resolve(__dirname, configFile))) {
    config = require(path.resolve(__dirname, configFile)) || {};
}

// version & help opitions
program
    .version(config.version || 'v1.0.0', '-v --version')
    .option('-h --help', 'output usage information')

// init
program
    .command('init')
    .description('initialize a hello-cli project')
    .action(() => {
        init();
    });

// 读取并解析命令行参数
program.parse(process.argv);
```

- 关于command模块各个方法的解说网上有很多，这里就不多赘述了，下面我们来看看代码敲完后的效果。

- 首先输入`hello -v && hello -h`查看版本号与帮助页面的样子：
<center>![7.png](https://i.loli.net/2021/01/24/ApjXyJuO6KFtrlG.png)</center>

- 然后输入指令`hello init`：
<center>![8.png](https://i.loli.net/2021/01/24/3PspGJv8xuKLMCH.png)</center>

- 至此Hello CLI的整体结构就基本完善了。
<center>![9.png](https://i.loli.net/2021/01/24/i8obHtf5JpxjSLR.png)</center>


二、结语
-----------------------------

- 借用他人的话总结一下就是：`bin/index.js`或者其他自定义的相关文件是主入口文件，而`packages/commands`专门放主要的命令功能逻辑，根据命令模块划分，比较细的功能实现可以抽成组件放在`packages/lib`文件夹中，剩余的配置，以及模板等放与cli同名文件夹中。 ——[node.js 命令行工具(cli)](https://juejin.cn/post/6844903603950338062)

- 总体来看，搭建一个简单的CLI还是很简单，困难的是如何划分功能模块以及完成想要赋予CLI的功能的开发。这些我都还在学习Orz

- 参考：
	- [用Node.js开发一个Command Line Interface (CLI)](https://zhuanlan.zhihu.com/p/38730825)
	- [node.js 命令行工具(cli)](https://juejin.cn/post/6844903603950338062)
	- [Commander.js 中文文档(cli必备)](https://juejin.cn/post/6844903857324064782)

上传于2021-01-24

[1]: https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwiH7vSBw7PuAhUECqYKHZ-CBu0QmhMwHXoECCkQAg&url=https%3A%2F%2Fzh.wikipedia.org%2Fzh-cn%2F%25E5%2591%25BD%25E4%25BB%25A4%25E8%25A1%258C%25E7%2595%258C%25E9%259D%25A2&usg=AOvVaw21SXkXUw9KxrZHKK6_V47B