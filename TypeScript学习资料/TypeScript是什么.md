# 1.TypeScript介绍

<hr />

## 1.1TypeScript是什么

- TypeScript (简称:TS) 是JavaScript的`超集`(JavaScript有的TypeScript都有)
- TypeScript=Type+JavaScript(在JS基础之上,为JS添加类型支持)
- TypeScript是微软开发的开源编程语言,可以在任何可以运行JavaScript的地方运行

```typescript
// TypeScript代码:有明确的类型,即:number(数值类型)
let age1: number = 18
// JavaScript代码:无明确类型
let age2 = 18
```

<hr />

## 1.2TypeScript为什么要为JS添加类型支持?

背景: JS的类型系统存在"先天缺陷",JS代码中绝大部分错误都是类型错误(Uncaught `Type`Error)

问题: 增加了找Bug,改Bug的时间,严重影响开发效率



__从编程语言的动静来区分,TypeScrpit属于静态类型的编程语言,JS属于动态类型的编程语言.__

静态类型: 编译期做类型检查; 动态类型: 执行器做类型检查.

代码编译和代码执行的顺序: 1.编译 2.执行



对于`JS`来说: 需要等到代码真正去执行的时候才能发现错误(晚)

对于`TS`来说: 在代码编译的时候(代码执行前)就可以发现错误(早)

并且,配合VScode等开发工具,`TS`可以提前到在编写代码的同时发现错误,减少找Bug,改Bug的时间.

<hr />

## 1.3TypeScript想必JS的优势

1. 更早(写代码的同时)发现错误,减少找Bug,改Bug的时间,提升了开发效率.
2. 程序中任何位置都有代码提示,随时随地安全感,增强了开发体验.
3. 强大的类型系统提升了代码的可维护性,使得重构代码更加容易.
4. 支持最新的`ECMAScrpit`语法,优先体验最新语法,让你走在前端技术的最前沿.
5. TS`类型推断`机制,不需要任何在代码中的每个地方都显示标注类型,让你享受优势的同时,尽量降低了成本.



__除此之外,Vue3源码使用TS重写、Angular默认支持TS、React与TS完美配合,TypeScript已成为大中型前端项目的首先编程语言.__

