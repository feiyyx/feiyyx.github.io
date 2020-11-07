---
title: React在TS环境下的爬坑手册<一>——Redux篇
tags: 
      - React
      - TypeScript
      - Redux
---

React及Redux在TS环境下的爬坑手册<一>
=================================

前言
-------------------------

- 想在入职前熟悉一下React的在TypeScript下的写法，以及复习一下TypeScript的各种用法。谁知道开始写了以后才发现这玩意真的是深不见底……Orz<!--more-->

爬坑开始
----------------

### 创建TypeScript React项目/初涉redux

- 这个还蛮简单的，使用create-react-app对着TypeScript的手册慢慢输过去就建好了。提供一个传送门：https://typescript.bootcss.com/tutorials/react.html

- 对着这个TypeScript手册进行操作，还可以顺便把TypeScript环境下的React-Redux熟悉一下，跟着手册走基本上不会遇到什么问题。如果想要更深入了解Redux和React之间的联系，并且觉得不想看英文文档，建议查看[Redux中文文档][1]，耐心看完会有挺多收获的。不过英文文档啃下来收货应该会更大的样子，毕竟是一直在更新的。

- 该手册中教程源码的GitHub仓库：https://github.com/microsoft/TypeScript-React-Starter。

- 还可以配合这个[【深入了解TypeScript-React JSX】][2]食用。

### Redux中store数据类型声明（未使用Hook）

- 由于Redux的store数据是**一个单一的** state 对象，所以在建立组件与store内state映射进行映射的时候，在JSX中直接使用类似这样↓的写法是完全没有问题：
```
const mapStateToProps = (state) => {
  return {
    x: state.x
  }
}
const mapDispatchToProps = (dispatch) => {
  return {
    y: (...args) => dispatch(actions.y(...args)),
  }
}
```
- 但是当使用TypeScript的时候，我们需要为state加上一个类型的声明，这个时候就行不通了，在这个时候我们有两种办法。

#### 第一种办法是我自己根据TS的性质想到的

- 这个办法需要建立一个types文件夹，收录各个数据自己本身的类型，之后再在该文件夹下新建一个AppTypes.ts(为了与主文件App的名称保持一致，实际上想叫什么名字都行)把它们整合起来：

```
export type xState = {
    text: string;
}

export type ytate = string;

export type AllState = {
   x: xState;
   y: yState;
}
```

- state数据映射方面的补充：https://imweb.io/topic/5a426d32a192c3b460fce354

- 之后修改成这样就没问题了：

```
const mapStateToProps = (state: AllState) => {
  return {
    x: state.x
  }
}
const mapDispatchToProps = (dispatch: Dispatch<action.AlphabetActionTypes>) => {
  return {
    y: (...args) => dispatch(actions.y(...args)),
  }
}
```
- 这里的action.AlphabetAction是在action的type中声明的：

```
export const X = 'X'
export const Y = 'Y'

export interface xState {
    text: string;
}

export type ytate = string;

interface XAction {
  type: typeof X
  payload: xState
}

interface YAction {
  type: typeof Y
  payload: yState
}

export type AlphabetActionTypes = XAction | YAction
```

- 本来我是用interface来对所有类型进行声明，但是interface有一点不好就是它不能声明基本变量名，所以后来我也使用了type来对 其他不能使用interface进行声明的类型 进行声明。

- type和interface的区别有几篇文章讲得蛮好，一篇是StackOverflow[这一篇][3]，一篇是掘金的[这篇文章][4]，还有一篇是简书的[这篇][5]，结合起来看看挺不错的。

#### 第二种方法是使用TypeScript提供的实用类型(utility types)

- 本着刨根问底的精神，我又去谷歌了一下更优雅的实现方法，后面在Redux的[英文文档][6]里发现了解决方案：

> We now need to generate the root reducer function, which is normally done using combineReducers. Note that we do not have to explicitly declare a new interface for RootState. We can use ReturnType to infer state shape from the rootReducer.

```
import { systemReducer } from './system/reducers'
import { chatReducer } from './chat/reducers'

const rootReducer = combineReducers({
  system: systemReducer,
  chat: chatReducer
})
// 重点在这一句↓↓↓
export type RootState = ReturnType<typeof rootReducer>
```

- 使用这个实用类型我们就可以直接获取combine之后的所有reducers的类型，就不用再使用第一种笨笨的方法了。

结语
------------

- 学习了Redux的使用方法之后，我觉得最大的感受就是它将state的改动的执行(reducer)与描述(action)给区分开来，也就是使用纯函数来执行修改，由纯函数的性质保持数据的只读性。而且state的reducer函数就相当于给这个state进行命名叻，这个让我在学习过程中承受了很多不能承受之痛XD，特别是与TS的类型声明结合起来以后www，看来我还是太蠢了。

- 至于三大原则的单一数据原则我还没有实践到这一步，没法感受同构应用开发的优越性，等我尝试使用SSR的写法的时候再去好好感受一下这个的便利性。

- 定下一个小目标，下一篇爬坑手册记录一下用React16.8的新特性——Hook来重构项目的坑，顺便学习一下React在TS中类型断言的食用方法。

- 参考文档：
    - Redux [中文文档][1]
    - Redux [英文文档][6]
    - GitBook [深入了解TypeScript 中文版 — React JSX][2]
    - Stack Overflow [TypeScript:Interfaces vs Types][3]
    - 掘金 [Typescript 中的 interface 和 type 到底有什么区别][4]
    - 简书 [typeScript interface和type区别][5]
    
<br>
上传于2020-08-19


  [1]: https://cn.redux.js.org/
  [2]: https://jkchao.github.io/typescript-book-chinese/jsx/reactJSX.html#%E5%BB%BA%E7%AB%8B
  [3]: https://stackoverflow.com/questions/37233735/typescript-interfaces-vs-types
  [4]: https://juejin.im/post/6844903749501059085
  [5]: https://www.jianshu.com/p/555e6998af36
  [6]: https://redux.js.org/recipes/usage-with-typescript#overview