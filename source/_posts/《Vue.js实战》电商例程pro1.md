---
title: 《Vue.js实战》电商例程pro1
tags: 
      - 前端
      - Vue
---

《Vue.js实战》电商例程pro1
=================================

〇、问题描述
-------------------------

- 把电商的实战例程敲完并阅读理解源码后，开始思考课后题：
![1.png-36.2kB][1]
<!--more-->

一、问题思考 & 问题解决
-----------------------------

- 由于例程原先给进行判断的函数和data如下：
```
<template>
    <div v-show="list.length">
        <div class="list-control">
            <div class="list-control-filter">
                <span>品牌：</span>
                <span   
                    class="list-control-filter-item"
                    :class="{ on: item === filterBrand }"
                    v-for="item in brands"
                    @click="handleFilterBrand(item)">{{ item }}</span>
            </div>
            ...（省略）
        </div>
    </div>
</template>
<script>
import Product from '../components/product.vue';
export default {
    components: { Product },
    data() {
        return {
            filterBrand: '',
        }
    },
    methods: {
        ...
        handleFilterBrand(brand) {
            //单次点击选中，再次点击取消选中
            if(this.filterBrand === brand) {
                this.filterBrand = '';
                console.log(brand);
            } else {
                this.filterBrand = brand;
            }
        },
        ...
 
    },
    computed: {
        ...
        filteredAndOrderedList() {
            //复制原始数据 为es6语法
            let list = [...this.list];
            //按品牌过滤
            if(this.filterBrand !== []) {
                list = list.filter(item => item.brand === this.filterBrand);
            } 
            //按颜色过滤
            console.log(list);
            if(this.filterColor !== '') {
                list = list.filter(item => item.color === this.filterColor);
            }
            //排序
            if(this.order !== '') {
                if(this.order === 'sales') {
                    list = list.sort((a,b) => b.sales - a.sales);
                } else if (this.order === 'cost-desc') {
                    list = list.sort((a,b) => b.cost - a.cost);
                } else if (this.order === 'cost-asc') {
                    list = list.sort((a,b) => a.cost - b.cost);
                }
            }
            
            return list;
        }
    },
    ...
}
</script>

```
- 所以想要将单选改成多选，首先要将filterBrand改成一个数组，用于存储点击时存储的brand的名字

- 然后将methods中的handleFilterBrand()修改成为能够存储数组并且判断是否重复的函数。一开始是想写一个能够适用于所有类型的数组的过滤函数，然后发现目前的Js水平并达不到，于是我只有用例程main.js中所给的那个数组排重的函数：
```
 //数组排重
getFilterArray(array) {
    const res = [];
    const json = {};
    for (let i = 0; i < array.length; i++) {
        const _self = array[i];
        //在if中将第i项移入json中成为key，然后赋值1使之存在，然后利用json的性质，若存在则返回true
        if (!json[_self]) {
            res.push(_self);
            json[_self] = 1;
        }
    }
    return res;
}
```

- 然后用indexOf()方法来判断是否存在于数组内，并且使用返回后的index值结合splice(index, 1)来将已有的品牌名删除，没有的话则使用push方法push进数组中。修改后的handleFilterBrand()函数如下
```
handleFilterBrand(brand) {
    //单次点击选中，再次点击取消选中
    let checkFlag = this.filterBrand.indexOf(brand);
    
    if(checkFlag > -1)//判断是否存在
        this.filterBrand.splice(checkFlag, 1);
    else
        this.filterBrand.push(brand);

    //组件之间相互调用需要这个前缀
    //如果该功能用的多的话，最好封装成为一个方法。
    this.$options.methods.getFilterArray(this.filterBrand);
},
        
```
- 然后考虑模板中on样式的启用，由于这个是需要返回一个true，所以我的思考是将等式判断修改为函数，然后函数内进行val值是否存在的判断来返回T or F。

- 虽然期间想过使用find()或者filter()，但是似乎这些是通过函数判断后才返回一个数组，这样的函数我思考良久没有想出合适的写法，于是再次使用indexOf()，并且通过判断indexOf()方法返回值的正负来判断是否存在。

- 最后写出来的函数是这个样子：
```
checkBrands(item) {
    if (this.filterBrand.indexOf(item) > -1)
        return true;
    else return;
},
```
然后template中改写成：
```
<div class="list-control-filter">
    <span>品牌：</span>
    <span   
        class="list-control-filter-item"
        :class="{ on: checkBrands(item) }"
        v-for="item in brands"
        @click="handleFilterBrand(item)">{{ item }}</span>
</div>
```

二、最终结果
--------------------

- 虽然过程看上去挺简单的，但是我也思考了挺长时间，可能是自己还是比较菜吧，不过还是比较优雅的将pro1给的业务将逻辑抽象出来了，最终呈现对比效果如下（图片用的是《Vue.js实战》中的所给地址，也许是服务器出了点小问题导致图片api没有对接上）：
![2.png-233.7kB][2]

- 选择后

![3.png-29.1kB][3]

三、小总结
---------------------------------------

- 这次课后题我对JavaScript中的Array类型的各种方法都进行使用并了解了一下，通过动手实践，算是亲身体会它们【filter(), map(), find(), indexOf()】的用法，算是夯实了一下自己关于JS中Array的基础。



上传于2018-11-18


 


  [1]: http://static.zybuluo.com/feiyyx/rw8gzgzjjim5vn59sbgj1qem/1.png
  [2]: http://static.zybuluo.com/feiyyx/yyftn6l52cd4fwn0r43at8ue/2.png
  [3]: http://static.zybuluo.com/feiyyx/m09m4kymeou8cwjhnulwdyif/3.png