---
title: CVTE网上笔试题之编程题（一）
tags: 
      - JavaScript
      - 面试
---

CVTE网上笔试题之编程题（一）
=================================

一、前言
-------------------------

- 前几天参加CVTE网上的笔试题，除了基础选择题感觉做得和shit一样，后面的编程题感觉也做的不太好。怎么说呢，有思路但是会一时半会想不起该使用的函数，或者是想得起来函数却不记得使用方法，所以正好借此机会复现一下两道题，并且复习一下JS的常用函数，为以后面试/笔试做准备。<!--more-->

二、正题
-------------------------

- 第一题的题目是这样的：输入一个数组和一个数字，根据数字找出第n小的数字。
e.g. fn([1，1，3，4，7]，2]=3。

- 看到这题我的思路是：利用sort()将数组中的数字升序排列，然后将原数组拷贝一份，并且在拷贝过程中使用indexOf()排除数组中重复数字，最后根据n-1来找到第n小的数字。但是当时我太急了，第一次网上笔试还见到这样的阵势，感觉自己写的错漏百出，具体怎么写的忘记了，不过可以肯定的是一大堆Bug，后悔没有早点采用一次过编程进行训练。

```JavaScript
//使用indexOf排重
var fn = (arr, n) => {
    let tempArr = [...arr].sort((a,b) => a-b);
   	let output = new Array();
    
    //数组排重
    tempArr.filter((val) => {
        if(output.indexOf(val) === -1)
            output.push(val);
        });
    
    return output[n-1];
}
```
- 输出结果：
<br>
![1.png-22.8kB][1]
- 然后现在思考了一下，使用indexOf()效率会不会有些低下。恰好以前学vue的时候写过一个Deemo，虽然是手把手教会的，但是还是学到了不少东西，例如使用Obj的键对来排重。

- 根据Object的键对唯一性的思路更新一下程序，让程序更优雅好看（这两次做到了一次过XD，写完处理完语法错误之后看正确输出一气呵成)：
```JavaScript
//Obj键对排重
var fn2 = (arr, n) => {
    let tempArr = [...arr].sort((a,b) => a-b);
   	let output = new Array();
   	let tempObj = {};
    
    //数组排重
    tempArr.map((val) => {
        if(!tempObj[val])
            output.push(val);
            tempObj[val] = 1;
        });
    
    return output[n-1];
}
    
```
- 输出结果：
<br>
![2.png-16.2kB][2]
- 后来去查了一下，若是使用Obj键对排重会有一个缺陷，因为Obj的key只能为字符串，所以当数组中存在Number和String多类型混合的时候就会出现错误。这个时候就要用别的方法了。网上给的例子是使用typeof val + val拼凑，然后使用filter()的性质，返回1时才将值放入新数组。例子如下：

```JavaScript
var array = [1, 2, 1, 1, '1'];

function unique(array) {
    var obj = {};
    return array.filter(function(item, index, array){
        return obj.hasOwnProperty(typeof item + item) ? false : (obj[typeof item + item] = true)
    })
}

console.log(unique(array)); // [1, 2, "1"]
```

三、后记（笔记）
-------------------------

- 最后感觉自己真的菜菜的，这么简单的题目都要偷偷查一下手册才能写出来，真的是基础不牢地动山摇啊……前端之路任重而道远。最近几天也都在看mdn上的JS文档夯实基础，顺带在博文后面也贴一贴吧，如果哪天回看这篇还能再复习一下。

- 本次写题用到的方法（节选自MDN中文）

- `filter()`： 方法创建一个新数组, 其包含通过所提供函数实现的测试的所有元素。 
 - `var new_array = arr.filter(callback(element[, index[, array]])[, thisArg])`
 - **接受四个参数**
 - `callback`
    用来测试数组的每个元素的函数。调用时使用参数 (element, index, array)。
    返回true表示保留该元素（通过测试），false则不保留。它接受三个参数：
 - `element`
当前在数组中处理的元素。
 - `index` 可选
正在处理元素在数组中的索引。
 - `array` 可选
调用了filter的数组。

```
var words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];

const result = words.filter(word => word.length > 6);

console.log(result);
// expected output: Array ["exuberant", "destruction", "present"]

```
<br><br>
 - `indexOf()`方法返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回-1。
   - `arr.indexOf(searchElement)`
   - `arr.indexOf(searchElement[, fromIndex = 0])`
   - **接受两个参数**
   - `searchElement`
要查找的元素
   - `fromIndex`
开始查找的位置。如果该索引值大于或等于数组长度，意味着不会在数组里查找，返回-1。如果参数中提供的索引值是一个负值，则将其作为数组末尾的一个抵消，即-1表示从最后一个元素开始查找，-2表示从倒数第二个元素开始查找 ，以此类推。 注意：如果参数中提供的索引值是一个负值，并不改变其查找顺序，查找顺序仍然是从前向后查询数组。如果抵消后的索引值仍小于0，则整个数组都将会被查询。其默认值为0。
```
var beasts = ['ant', 'bison', 'camel', 'duck', 'bison'];

console.log(beasts.indexOf('bison'));
// expected output: 1

// start from index 2
console.log(beasts.indexOf('bison', 2));
// expected output: 4

console.log(beasts.indexOf('giraffe'));
// expected output: -1

```

- `map()` 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。
  - `var new_array = arr.map(function callback(currentValue[, index[, array]]) {
 /* Return element for new_array*/ }[, thisArg])`
  - `callback`
  - **接受三个参数**：
  - `currentValue`
callback 数组中正在处理的当前元素。
  - `index`可选
callback 数组中正在处理的当前元素的索引。
  - `array`可选
callback  map 方法被调用的数组。
```
var words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];

const result = words.filter(word => word.length > 6);

console.log(result);
// expected output: Array ["exuberant", "destruction", "present"]

```

<br>
上传于2019-03-01


  [1]: http://static.zybuluo.com/feiyyx/3yv354kurgcgdb6cmg6f4i8i/1.png
  [2]: http://static.zybuluo.com/feiyyx/rbkppjbza3c4bcfmkwd8n8nu/2.png