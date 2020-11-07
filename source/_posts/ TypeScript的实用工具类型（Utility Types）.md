---
title: TypeScript的实用工具类型（Utility Types）
tags: 
      - TypeScript
      - 前端
---

TypeScript的实用工具类型（Utility Types）
=================================

〇、想说的一些话
--------------

- 之前学习TypeScript的时候发现TS中文网已经一年多没更新了，虽然大部分都和微软的官方文档差不多，但是还是有略微的差别，如果想学TS我的建议是打开英文文档，看一遍中文看一遍英文，以英文文档为主。我就是这么看过来的。完全查看英文文档的话也不是不行，不过如果没有母语水平还是推荐对照来看比较快。<!--more-->

- 官方文档（截止至2019年12月1日）已经更新到了3.7版本，也推出了一个在TS中文站里没有的页面——[Utility Types][1]，根据看到的其他人的翻译，我也把它称为**实用工具类型**，搜索了一下发现好像只在gitbook里有一篇收录了以前增加的实用工具类型，不太完整，于是我决定趁着自己学习的时候能够看得进英文文档的时候把它给翻译了。以下进入正文：

一、介绍
-------------------------

- TypeScript提供了几种实用工具类型，以方便进行常见的类型转换。 这些实用工具类型在全局范围内均可使用。

### Table of contents
 - Partial&lt;T&gt;
 - Readonly&lt;T&gt;
 - Record&lt;K,T&gt;
 - Pick&lt;T,K&gt;
 - Omit&lt;T,K&gt;
 - Exclude&lt;T,U&gt;
 - Extract&lt;T,U&gt;
 - NonNullable&lt;T&gt;
 - Parameters&lt;T&gt;
 - ConstructorParameters&lt;T&gt;
 - ReturnType&lt;T&gt;
 - InstanceType&lt;T&gt;
 - Required&lt;T&gt;
 - ThisType&lt;T&gt;

二、实用工具类型介绍
---------------

### Partial&lt;T&gt;

- 构造一个类型T，将T的所有属性设置为**可选**。 该工具将返回一个表示输入类型(T)的所有子集的类型。

- Example:
```
interface Todo {
    title: string;
    description: string;
}

function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
    return { ...todo, ...fieldsToUpdate };
}

const todo1 = {
    title: 'organize desk',
    description: 'clear clutter',
};
//todo1 = { title: 'organize desk', description: 'clear clutter' }


const todo2 = updateTodo(todo1, {
    description: 'throw out trash',
});
//todo2 = { title: 'organize desk', description: 'throw out trash' }
```

### Readonly&lt;T&gt;

- 构造一个所有属性都设置为只读的类型T，这意味着**无法**再次对所构造类型的属性**进行赋值**。

- Example:
```
interface Todo {
    title: string;
}

const todo: Readonly<Todo> = {
    title: 'Delete inactive users',
};

todo.title = 'Hello'; // Error: cannot reassign a readonly property
```

- 该工具对于表示在运行时会失败的赋值表达式很有用（例如，当尝试对冻结对象属性再次赋值时）。

- Object.freeze
```
function freeze<T>(obj: T): Readonly<T>;
```

### Record&lt;K,T&gt;

- 构造一个属性名为K类型，属性值为T类型的类型。该工具可用于将一个类型的属性映射到另一个类型中。

- Example:
```
interface PageInfo {
    title: string;
}

type Page = 'home' | 'about' | 'contact';

const x: Record<Page, PageInfo> = {
    about: { title: 'about' },
    contact: { title: 'contact' },
    home: { title: 'home' },
};
```

### Pick&lt;T,K&gt;

- 通过从类型T**选取**属性K的集合来构造类型。

- Example:
```
interface Todo {
    title: string;
    description: string;
    completed: boolean;
}

type TodoPreview = Pick<Todo, 'title' | 'completed'>;

const todo: TodoPreview = {
    title: 'Clean room',
    completed: false,
};
```
### Omit&lt;T,K&gt;

- 通过从T中**选取**所有属性**然后删除**传入的属性K来构造新类型。

- Example:
```
interface Todo {
    title: string;
    description: string;
    completed: boolean;
}

type TodoPreview = Omit<Todo, 'description'>;

const todo: TodoPreview = {
    title: 'Clean room',
    completed: false,
};
```

### Exclude&lt;T,U&gt;

- 通过从T中**剔除**可赋值给U的属性来构造一个新类型。

- Example:
```
type T0 = Exclude<"a" | "b" | "c", "a">;  // "b" | "c"
type T1 = Exclude<"a" | "b" | "c", "a" | "b">;  // "c"
type T2 = Exclude<string | number | (() => void), Function>;  // string | number
```

### Extract&lt;T,U&gt;

- 通过从T中**挑选**可赋值给U的属性来构造一个新类型。

- Example:
```
type T0 = Extract<"a" | "b" | "c", "a" | "f">;  // "a"
type T1 = Extract<string | number | (() => void), Function>;  // () => void

```

### NonNullable&lt;T&gt;

- 通过从T中**去除**`null`和`undefined`来构造一个新类型

- Example:
```
type T0 = NonNullable<string | number | undefined>;  // string | number
type T1 = NonNullable<string[] | null | undefined>;  // string[]
```

### Parameters&lt;T&gt;

- 构造一个关于函数类型T的**参数类型**的元组类型。

- Example:
```
declare function f1(arg: { a: number, b: string }): void;

type T0 = Parameters<() => string>;  // []
type T1 = Parameters<(s: string) => void>;  // [string]
type T2 = Parameters<(<T>(arg: T) => T)>;  // [unknown]
type T4 = Parameters<typeof f1>;  // { a: number, b: string }
type T5 = Parameters<any>;  // unknown[]
type T6 = Parameters<never>;  // any
type T7 = Parameters<string>;  // Error
type T8 = Parameters<Function>;  // Error
```

### ConstructorParameters&lt;T&gt;

- 通过ConstructorParameters <T>类型，我们可以**提取**构造函数类型的所有参数类型。 它会生成构造函数所具有的所有参数类型的元组类型（如果T不是函数，则不返回）。

- Example:
```
type T0 = ConstructorParameters<ErrorConstructor>;  // [(string | undefined)?]
type T1 = ConstructorParameters<FunctionConstructor>;  // string[]
type T2 = ConstructorParameters<RegExpConstructor>;  // [string, (string | undefined)?]
```

### ReturnType&lt;T&gt;

- 构造一个由函数T的返回类型组成的新类型。

- Example:
```
declare function f1(): { a: number, b: string };
type T0 = ReturnType<() => string>;  // string
type T1 = ReturnType<(s: string) => void>;  // void
type T2 = ReturnType<(<T>() => T)>;  // {}
type T3 = ReturnType<(<T extends U, U extends number[]>() => T)>;  // number[]
type T4 = ReturnType<typeof f1>;  // { a: number, b: string }
type T5 = ReturnType<any>;  // any
type T6 = ReturnType<never>;  // any
type T7 = ReturnType<string>;  // Error
type T8 = ReturnType<Function>;  // Error
```

### InstanceType&lt;T&gt;

- 构造一个由构造函数T的**实例类型**组成的新类型

- Example:
```
class C {
    x = 0;
    y = 0;
}

type T0 = InstanceType<typeof C>;  // C
type T1 = InstanceType<any>;  // any
type T2 = InstanceType<never>;  // any
type T3 = InstanceType<string>;  // Error
type T4 = InstanceType<Function>;  // Error
```

### Required&lt;T&gt;

- 构造一个将T的所有属性都设置为`required`的新类型。

- Example:
```
interface Props {
    a?: number;
    b?: string;
};

const obj: Props = { a: 5 }; // OK

const obj2: Required<Props> = { a: 5 }; // Error: property 'b' missing
```

### ThisType&lt;T&gt;

- 该工具不会返回转换后的类型。 相反，它是一个用作上下文类型中键入this类型的标记（*提供通过上下文类型控制 this 类型的方法——来自参考资料*）。 注意，必须启用--noImplicitThis标志才能使用此工具。

- Example:
```
// Compile with --noImplicitThis

type ObjectDescriptor<D, M> = {
    data?: D;
    methods?: M & ThisType<D & M>;  // Type of 'this' in methods is D & M
}

function makeObject<D, M>(desc: ObjectDescriptor<D, M>): D & M {
    let data: object = desc.data || {};
    let methods: object = desc.methods || {};
    return { ...data, ...methods } as D & M;
}

let obj = makeObject({
    data: { x: 0, y: 0 },
    methods: {
        moveBy(dx: number, dy: number) {
            this.x += dx;  // Strongly typed this
            this.y += dy;  // Strongly typed this
        }
    }
});

obj.x = 10;
obj.y = 20;
obj.moveBy(5, 5);
```

- 在上面的示例中，makeObject的参数中的methods对象具有包含ThisType<D＆M>的上下文类型；因此，在methods对象内的方法中，方法的this类型为{x：number，y：number}＆{moveBy (dx: number，dy: number) : number}。 请注意methods属性的类型是如何同时成为方法中this类型的推断目标和来源的。

- ThisType <T>标记接口仅仅是在lib.d.ts中声明的一个空接口。 除了在对象字面量的上下文类型中被识别外，该接口的作用类似于任何空接口。

二、结语
-------------------------

- 这些实用工具类型看上去很好用的样子，先mark翻译一下，未来有需要就用起来～。

- 如果老哥要转载的话请高抬贵手给个通往我博客的链接嗷，谢谢了～顺带推荐一本找资料时候发现的书，个人感觉很不错：[《深入理解TypeScript》][2]

- 参考资料
    - https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Utility%20Types.html
    - https://jkchao.github.io/typescript-book-chinese/typings/thisType.html

<br>
上传于2019-12-01


  [1]: http://www.typescriptlang.org/docs/handbook/utility-types.html
  [2]: https://jkchao.github.io/typescript-book-chinese/#why