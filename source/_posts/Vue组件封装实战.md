---
title: Vue组件封装实战
tags: 
      - Vue
      - 前端
---

Vue组件封装实战
=================================

〇、楔子
-------------------------

- 最近的项目有需要将某个组件从项目整体中抽离成npm包，然后通过Vue install组件形式从npm包中引用起来，以及如何与组件内部的值进行双向绑定，写篇文章记录一下。<!--more-->


一、封装过程
--------------------------

- 创建的过程网上都可以找到，最后目录类似于这样：

![1.png](https://i.loli.net/2021/05/23/zufEmLAq3aYKIg8.png)

- 以及需要修改一下package.json里的main文件

- 从packages文件夹说起吧，里面的目录如下，存储的是我们需要封装的组件。

![2.png](https://i.loli.net/2021/05/23/NRXOjn21CgZTYue.png)

- ComponentA代码：

```vue
<template>
  <div id="app">
    This is AAAAA Component.
  </div>
</template>

<script>
export default {
  name: 'AComponent',
  components: {
  }
}
</script>

<style>
</style>

```

- ComponentA文件夹下`index.js`文件代码，这里的代码实际上是我预留给按需加载使用的，不过我还没摸索清楚如何进行按需加载，实践证明不写这个文件也可以进行组件的注册。：

```javascript
import AComponent from './AComponent.vue';

// vue插件必须有一个公开的install(Vue, options)方法。
// 这个方法的第一个参数是 Vue 构造器，第二个参数是一个可选的选项对象
AComponent.install = function(Vue) {
		// Vue会在Vue.use()时传递进来，options是选项对象。Vue.use(Modal, { someOption: true })
    Vue.component(AComponent.name, AComponent);
}

export default AComponent;
```

- 在整个`package`文件夹下，添加一个`index.js`，文件，用来注册所有的组件：

```javascript
import AComponent from './AComponent/AComponent.vue';
import BComponent from './BComponent/BComponent.vue'

const components = [
    AComponent,
    BComponent
]

const install = function(Vue) {
    if(install.installed) return;
    components.map(item => { Vue.component(item.name, item) })
    install.installed = true;
} 

if(typeof window !== 'undefined' && window.Vue) {
    install(window.Vue);
}

export default {
    install,
    AComponent,
    BComponent
}
```

- 之后就可以使用了：

  
```vue
<template>
  <div id="app">
    <AComponent />
  </div>
</template>

<script>
import FComponent from '@feiyyx/component'

export default {
  name: 'App',
  components: {
    FComponent.AComponent,
  }
}
</script>
<style>
</style>
```

- 因为没有按需加载，而且应该因为Vue自身的原因，直接使用如下的方法是会报undefined的错的，使用`console.log`输出来看也是undefined，压根就没有执行install函数。最后的这个组件的效果很简单，就不给大家看了。
> import { ACompoennt } from '@feiyyx/components'    // 会报错的引入方法

- 在上面出现的`install`函数应该就是Vue官方给插件提供的钩子函数，用来给Vue.use()方法调用插件内部的install方法，并将Vue实例作为参数传入，从[网上扒来](https://segmentfault.com/a/1190000022802059)的Vue.use()方法源码以及注释如下

```javascript
export function initUse (Vue: GlobalAPI) {
    // 注册一个挂载在实例上的use方法
    Vue.use = function (plugin: Function | Object) {
        // 初始化当前插件的数组
        const installedPlugins = (this._installedPlugins || (this._installedPlugins = []))
        // 如果这个插件已经被注册过了，那就不作处理
        if (installedPlugins.indexOf(plugin) > -1) {

            return this

        }

        ...
        
        // 重点来了哦！！！
        if (typeof plugin.install === 'function') {
        // 当插件中install是一个函数的时候，调用install方法，指向插件，并把一众参数传入
            plugin.install.apply(plugin, args)

        } else if (typeof plugin === 'function') {
        // 当插件本身就是一个函数的时候，把它当做install方法，指向插件，并把一众参数传入
            plugin.apply(null, args)

        }
        
        // 将插件放入插件数组中
        installedPlugins.push(plugin)

        return this
    }
}
```




二、双向绑定
--------------------------

- 如何做到像这样在组件ComponentB上v-model绑定值呢？首先我们先要声明要v-model绑定的值（废话）。

```vue
// App.vue
<template>
  <div id="app">
    <AComponent v-model="testValue" />
    <p>Combined value is: {{ testValue }}</p>
  </div>
</template>
<script>
// ...
export default {
  // ...
  components: {
    AComponent,
  },
  data() {
    return {
      testValue: ''
    }
  }
  // ...
}
</script>

<style>
</style>

```

- 然后在AComponent组件内部的props声明一个value，用来接受v-model绑定的父组件的值；同时在data里声明一个内部的变量，赋值为`this.childValue`，用来与父组件传入的值进行绑定。\<template\>也进行了相应的改。

```vue
// AComponent
<template>
  <div id="app">
    This is AAAAA Component.
    <input v-model="childValue"><!--新增input函数-->
  </div>
</template>
<script>
export default {
  name: 'AComponent',
  components: {
  },
  props: {
    value: {
      type: String,
      default: '',
    }
  },
  watch: {
    //监听prop传的value，如果父级有变化了，将子组件的childValue也跟着变，达到父变子变的效果
    childValue (newVal) {
      this.childValue = newVal
    },
    //监听childValue，如果子组件中的内容变化了，通知父级组件，将新的值告诉父级组件，我更新了，父级组件接受到值后页就跟着变了
    //参考官网：https://cn.vuejs.org/v2/guide/components-custom-events.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%BB%84%E4%BB%B6%E7%9A%84-v-model
    childValue (newVal) {
      this.$emit('input', newVal)
    }
  },
  data() {
    return {
      childValue: '',
    }
  }
}
</script>

<style>
</style>

```

- 之后通过watch来监听来自外部的父组件的值与子组件中与该值相同的data，然后使用`this.$emit`将组件内的值传递出去给父组件：

```vue
// AComponent
// ...
<script>
export default {
  // ...
  watch: {
    //监听prop传的value，如果父级有变化了，将子组件的childValue也跟着变，达到父变子变的效果
    childValue (newVal) {
      this.childValue = newVal
    },
    //监听childValue，如果子组件中的内容变化了，通知父级组件，将新的值告诉父级组件，我更新了，父级组件接受到值后页就跟着变了
    //参考官网：https://cn.vuejs.org/v2/guide/components-custom-events.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%BB%84%E4%BB%B6%E7%9A%84-v-model
    childValue (newVal) {
      this.$emit('input', newVal)
    }
  },
  // ...
}
</script>

<style>
</style>


```

- 这样就可以实现v-model双向绑定父子组件的值了。效果如下：

![3.png](https://i.loli.net/2021/05/23/AEqvIrOxs1XL3cR.png)

- 参考文章：
	- Vue封装npm组件
		- https://juejin.cn/post/6844903953180835847#heading-1
		- https://www.shangmayuan.com/a/b403a4d23efb4254812cf422.html
		- https://segmentfault.com/a/1190000022802059
	- Vue组件双向绑定
		- https://www.shangmayuan.com/a/b403a4d23efb4254812cf422.html

<br>
上传于2021-05-23