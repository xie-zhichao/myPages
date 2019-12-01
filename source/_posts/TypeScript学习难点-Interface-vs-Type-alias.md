---
title: TypeScript学习难点-Interface vs Type alias
date: 2019-05-30 17:49:30
tags:
 - TypeScript
 - JavaScript
categories: TypeScript
---
## 背景
初学TypeScript时，我遇到一个疑惑，interface和type alias 都能用于类型名称，去声明一个结构。它们有什么区别呢？分别适用于哪些场景呢？

官方语言文档里面没有找到相关的知识<sup>1</sup>。后来搜索到一篇文章<sup>2</sup>，通过各种示例讲解了这个问题。下面我写一下自己阅读之后梳理的知识点（具体的例子，这里不复述，见参考文档）。

## 共同点
- 都可以用于声明类型
- 都可以被扩展（extends），被实现（implements）
    > **此处说明一下，TS语言参考文档中说，类型别名不能被继承和实现，我经过测试，发现是可以的。**

```typescript
    type A = {
      runner(time: number): void;
    }
    
    interface B extends A {
      name: string;
    }
    
    class A1 implements A {
      runner(time: number): void {
        console.log(time);
      }
    }
```

## 不同点
- interface不能声明字面类型，联合类型，元组
- 联合运算符定义的别名类型，不能被class实现，不能被interface继承
 
## 建议
- 尽量使用interface
- 联合类型、索引类型、映射类型、字面量类型等interface不能实现的，用type alias
>关于第一条，也有技术博文，提出只要团队内一致就行。

## 参考
1. [语言参考文档](https://www.tslang.cn/docs/home.html)
2. [Interface vs Type alias in TypeScript 2.7 – Martin Hochel – Medium](https://medium.com/@martin_hotell/interface-vs-type-alias-in-typescript-2-7-2a8f1777af4c)
