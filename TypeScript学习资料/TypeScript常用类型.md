# 3.TypeScript常用类型

#### 概述:



TypeScript是TS的超集,TS提供了JS所有的功能,并且额外增加了: `类型系统`.

- 所有的JS代码都是TS代码.

- JS有类型(比如,number/string等),但是JS`不会检查变量的类型`是否发生变化.而TS`会检查`

    ```javascript
    //错误示范
    let count = 18
    
    count = '20'
    
    count.toFixed()
    ```

    输出结果:

    ```powershell
     node .\第二组\03-js代码意外行为演示.js
    G:\TypeScript.git\TypeScript\代码意外行为演示\第二组\03-js代码意外行为演示.js:5
    count.toFixed()
          ^
    
    TypeError: count.toFixed is not a function
        at Object.<anonymous> (G:\TypeScript.git\TypeScript\代码意外行为演示\第二组\03-js代码意外行为演示.js:5:7)
        at Module._compile (node:internal/modules/cjs/loader:1241:14)
        at Module._extensions..js (node:internal/modules/cjs/loader:1295:10)
        at Module.load (node:internal/modules/cjs/loader:1091:32)
        at Module._load (node:internal/modules/cjs/loader:938:12)
        at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:83:12)
        at node:internal/main/run_main_module:23:47
    
    Node.js v20.9.0
    ```

    如果是TS代码:

    ```typescript
    let age: number = 18
    
    age = '20' // 错误提示: Type 'string' is not assignable to type 'number'.
    
    console.log(age)
    ```

    <hr />

    __怎么样,感受到TS的优势了吗?__:smile:

TypeScript类型系统的主要优势: 可以`显示标记出代码中意外行为`,从而降低了发生错误的可能性.



1. 类型注解
2. 常用基础类型

<hr />

## 3.1 类型注解

示例代码:

```typescript
let age: number = 18
```

说明: 代码中的:number就是类型注解.

作用: 为变量`添加类型约束`.比如,上述代码中,约定变量age的类型为number(数值类型).

__解释: 约定了什么类型,就只能给变量赋值该类型的值,否则就会报错.__

<hr />

## 3.2 常用的基础类型概述



可以将TS中的常用基础类型细分为两类: 1.JS已有类型  2.TS新增类型.

1. JS已有类型
    - 原始类型: `number/string/boolean/null/undefined/symbol`.
    - 对象类型: object(包括,数组、对象、函数等对象).

2. TS新增类型
    - 联合类型、自定义类型（类型别名）、接口、元组、字面量类型、枚举、void、any等。

<hr />

