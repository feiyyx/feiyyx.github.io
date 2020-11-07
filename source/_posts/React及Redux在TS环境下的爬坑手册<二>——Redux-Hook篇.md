---
title: React及Redux在TS环境下的爬坑手册<二>——Redux-Hook篇
tags: 
      - React
      - TypeScript
      - Redux
---

React及Redux在TS环境下的爬坑手册<二>
=================================

前言
-------------------------

- 学习使我快乐！<!--more-->

继续爬坑
----------------

### Redux中store数据类型声明（使用Hook）

- 在使用了react最新版内的hook后，以前写成的`class组件`要统统转换成`函数式组件`，所以我将原来的写法：

```
class App extends React.Component<Props, {}> {
  render() {
    return (
      ...
    );
  }
}
```

- 修改成了:

```
const App = (props:Props) => {
  return (
    ...
  );
}
```

- 当我们修改成了函数式组件后，我们就可以用上React的最新特性——Hook了。虽然最后使用的RootState的类型判断和之前未使用Hook的一样，不过在这里也是有新的变化，不然我也不会写下这篇文章了XD。

- 最令人欣喜的是在Redux中使用其官方订制的Hook可以将`mapStateToProps`、`mapDispatchToProps`和`connect`给省略掉，对照微软的[TypeScript-React-Starter][1]中的文件分类来看，我们可以省略掉containers文件夹，不过这样的话我们就需要在component也就是组件文件夹中的.tsx文件内引入constants，也就是我更习惯称为action-type文件夹内的action的类型。

- 还是拿微软这个举例，经过Redux的Hook改造后，最后该组件应该长这样：
```
...
/*--引入action的type--*/
import * as actionType from '../../action-type/index';
import { useSelector, useDispatch } from 'react-redux';

const App = (props:Props) => {
  /*-- 使用useSelector替代mapStateToProps --*/
  const languageName:string = useSelector((state: RootState ) => state.enthusiasmState.languageName)
  const enthusiasmLevel:number= useSelector((state: RootState ) => state.enthusiasmState.enthusiasmLevel)

  /*-- 使用useDispatch替代mapDispatchToProps --*/
  const dispatch = useDispatch();
  return (
    <div className="hello">
      <div className="greeting">
        Hello {languageName + getExclamationMarks(enthusiasmLevel)}
      </div>
      <div>
        <button onClick={() => dispatch({ type: actionType.DECREMENT_ENTHUSIASM })}>-</button>
        <button onClick={() => dispatch({ type: actionType.INCREMENT_ENTHUSIASM })}>+</button>
      </div>
    </div>
  );
}

...
export default App;
```

### 关于Redux的一个小贴士

- 在搜索如何更优雅地获取Store的类型的时候，我发现了一个十分优雅高贵的写法——使用react-redux@7.1.5库内预留的空白`interface DefaultRootState`。

<center>![1.jpg](https://i.loli.net/2020/09/05/VbRMaYedBqi9jAT.jpg)</center>

- 这个可供拓展的interface可以说是真的很贴心了，它是官方专门预留给程序员以 [declaration merging][2] 的技巧把字段声明注入到react-redux库预留好的空白`interface DefaultRootState`中，然后就可以直接从reat-redux中import出DefaultRootState然后开箱即用就好了。

- 我是这样使用的：上一章中我们讲了可以使用`ReturnType<typeof T>`获取到整合后所有Reducer的类型，然后我们可以将这个方法整合进store.ts

```
// store.ts
...
import { DefaultRootState } from "react-redux";

const rootReducer = combineReducers({x, y, z})
type RootState = ReturnType<typeof rootReducer>

declare module "react-redux" {
  // 使用Declaration Merging技巧拓展空白interface
  interface DefaultRootState extends RootState {}
}

const store = createStore(
    rootReducer,
    applyMiddleware(thunk)
 );

export default store;
```

- 之后在项目中就可以直接import出来使用了：

```
// project.ts
...
import { DefaultRootState } from "react-redux";

const App = () => {
  // 这样使用完全没问题
  const x:string = useSelector((state: DefaultRootState ) => state.x.name)

  return (
    <div className="hello">
      <div className="greeting">
        Hello { x }
      </div>
    </div>
  );
}
```


结语
------------

- 这次的文章主要是记录一下自己在学习react-redux中使用新特性Hook的历程，回过头有时间也许我会专门去学习一下Hook的原理，更深入的去了解React。

- 当然了，Vue3的学习也要提上日程了，也不知道尤大他们究竟修改了多少Api，虽然说学习使我快乐，但是有太多的东西要去学了，稍微有一点绝望呢，这还只是框架原理和业务代码，还有各种架构微服务等着去学呢Orz。

- 下一篇爬坑手册应该会写使用useDispatch()这个方法的学习过程，其他的就学到哪写到哪吧。

- 参考文档：
    - 掘金 [如何让 Redux 和 TypeScript 愉快的玩耍][3]
    - Dzone [Implementation of React-Redux Hooks With Typescript][4]
    - Qiita [react-reduxのHooks APIにGenericsは要らない][5]
    
<br>
上传于2020-09-06


  [1]: https://github.com/microsoft/TypeScript-React-Starter
  [2]: https://www.typescriptlang.org/docs/handbook/declaration-merging.html#merging-interfaces
  [3]: https://juejin.im/post/6844904154595328014
  [4]: https://dzone.com/articles/react-redux-hooks-with-typescript-in-2020
  [5]: https://qiita.com/Takepepe/items/6addcb1b0facb8c6ff1f