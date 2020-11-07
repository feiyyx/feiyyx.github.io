---
title: CVTE网上笔试题之编程题（二）
tags: 
      - JavaScript
      - 面试
---

CVTE网上笔试题之编程题（二）
=================================

一、前言
-------------------------

- 今天搞丢了东西有点不开心，看了会视频发现还是那么丧，所以还是学习来弥补心中的空虚算了……<!--more-->

二、正题
-------------------------

- 第二题的题目是这样的：输入一个地址和一组对象，根据对象的key换成相应的value。
  - e.g.**输入**：p/a/t/:id/h/:add/:add/add，{ id: 12，add: "addsth"｝ 

  - **输出**：p/a/t/12/h/addsth/addsth/add

- 看到这题我的思路是：利用split()（好像我当时把split和splice记混了Orz）将输入的地址字符串用'/'分割成数组，之后将传入的JSON对象的key值使用for...in取出来后依次为其前部加上":"并组成数组。然后对分割成的数组使用for循环与json对象的key对照，若全等于则将val值取代原数组值，最后将数组用join()再合成。

- 代码如下：
```JavaScript
let fn = (path, params) => {
    let tempPath = path.split('/');
    let tempParams = {...params};
    let tempParamsArray = new Array();
    
    for(let key in tempParams) {
        tempParamsArray.push(':' + key);
    }
    
    for(let i=0; i<tempPath.length;i++){
        if(tempParamsArray.indexOf(tempPath[i]) !== -1) {
                tempPath[i] = tempParams[tempPath[i].slice(1,tempPath[i].length)];
        }
    }

    output = tempPath.join('/');
    
    return output;
}
```
代码结果如下：
![1.png-24.3kB][1]

- 不过这样写显得代码又臭又长，有什么办法能精简代码，让它变得优雅一些呢？当时我感觉能用正则，但是我正则用的比较少就不太敢乱用，只有退而求其次使用上面的蠢办法。现在改良代码如下：
```JavaScript
let fn = (path, params) => {
    let tempPath = path.split('/');
    let tempParams = {...params};
    let output;
    let reg = /:(\w)*/;
    
    output = tempPath.map((val) => {
        if(tempParams[val.slice(1, val.length)])
            return val.replace(reg, tempParams[val.slice(1, val.length)]);
        else return val;
    });
    
    return output.join('/');
}
```
代码结果如下(这里忘记加join了)：
![2.png-17.3kB][2]


三、后记（笔记）
-------------------------

- 解题所用方法（除了Array的`splice()`方法，其他方法都返回另一个String/Array）：
- `split()`——**String**——该slice() 方法提取一个字符串的一部分，并**返回一新的字符串**。
  - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/split

- `slice()` 
  - **String**：该方法提取一个字符串的一部分，并**返回一新的字符串**。
    - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/slice
  - **Array**：该方法返回一个新的数组对象，这一对象是一个由 begin 和 end（不包括end）决定的原数组的浅拷贝。原始数组**不会被改变**。
    - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/slice

- `splice()`——**Array**——注：虽然该方法没用到但是对我来说易于其他两个相似的弄混。
  - ——该方法通过删除或替换现有元素来修改数组,并以数组形式返回被修改的内容。**此方法会改变原数组**。
  - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/splice
 
- `replace()` ——**String**——该方法返回一个由替换值（replacement）替换一些或所有匹配的模式（pattern）后的新字符串。
模式可以是一个字符串或者一个正则表达式，替换值可以是一个字符串或者一个每次匹配都要调用的函数。**原字符串不会改变**。
  - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace


  <br>
上传于2019-03-02


  [1]: http://static.zybuluo.com/feiyyx/6ovyclyhtsi6c9bca5ur3rk9/1.png
  [2]: http://static.zybuluo.com/feiyyx/1rqeahnb337cp5cq0pefss9c/2.png