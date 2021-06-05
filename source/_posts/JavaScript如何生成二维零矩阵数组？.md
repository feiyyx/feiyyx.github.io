---
title: JavaScript如何生成二维零矩阵数组？
tags: 
      - JavaScript
---

JavaScript如何生成二维零矩阵数组？
=================================

〇、楔子
-------------------------

-  逛论坛的时候偶然看到了这个问题：如何快速生成一个二维n\*m且全为0的矩阵？看着这个问题，我陷入了沉思，顺便借着这次机会就对数组的知识进行了一系列的学习\~。<!--more-->


一、快速生成二维零矩阵
--------------------------

- 如何快速生成一个二维n*m且全为0的矩阵？当时看到这个问题，我就啪，很快啊，写出了一行代码：

> const arr = new Array(5).fill( new Array(7).fill(0) );

- 然后我测试了一下，就傻眼了，这样生成的二维数组里的所有一维数组，都是对第一个数组的引用。所以当修改某一个数组里的某一个值时，其他的值也会随着改变：

<center>![1.png](https://i.loli.net/2021/06/05/GZCpEYNsBzi3JVb.png)</center>

- 这是为什么呢？带着满脑子的问号，我去MDN翻了翻关于`fill()`方法的资料，看到描述中有这样一句话：

> 当一个对象被传递给 fill方法的时候, 填充数组的是这个对象的引用。

- 好家伙，难怪会有这种事，感情`fill()`方法传对象的时候连浅复制都没有的，那好吧，既然这样那就只有祭出其他的可以遍历数组并且生成新数组的方法了！

```
// 改良后
const arr = new Array(5).fill(0).map(() => {
   return new Array(7).fill(0);
})
```

- 这种方式生成的就木有问题了：

<center>![2.png](https://i.loli.net/2021/06/05/jRCuZAqQIevYF69.png)</center>


二、生成多维零矩阵
--------------------------

- 既然前边说了如何生成二维0矩阵的数组，那我们可以扩展一下这个问题——如何生成多维零矩阵数组呢？

- 首先我们要先从生成二维矩阵数组的方法获取灵感，生成二维矩阵无外乎就是对一维矩阵的每一列进行拓展，那么我们就可以从这里入手，封装一个函数：

```
function multiArray(dimensions = [], fillValue = 0) {
    let res = [];
    const temp = [ ...dimensions ];
    if (
        Array.isArray(dimensions) &&
        dimensions.length &&
        dimensions.every(v => Number.isInteger(v))
    ) {
        while(temp.length >= 1) {
            let dimension = temp.pop();
            let arr = new Array(dimension).fill(fillValue);
            if (res.length) {
                res = arr.map(v => JSON.parse(JSON.stringify(res))); // 因为是简单多维数组，所以每一个维度都可以如此进行简单深拷贝
            } else {
                res = res.concat(arr);
            }
        }
    } else {
        throw new Error('ILLegal Input !')
    }
    
    return res;
}

multiArray([5,7,1]) // [[[0]*7]*5]
```

- 这样就可以进行生成简单的多维数组了，经过粗略的自测后也没有发现某一个子数组被引用的情况XD，最终效果如下：

<center>![3.png](https://i.loli.net/2021/06/05/UGiTkqEYo6vXchP.png)</center>


<br>
上传于2021-06-05