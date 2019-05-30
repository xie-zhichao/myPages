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

## 不同点
- interface不能声明基础类型，联合类型，元组
- 联合运算符定义的别名类型，不能被class实现，不能被interface继承
 
## 适应场景
- 都能满足使用时，团队内保持一致即可
- React Props/State，建议用type alias
- 编写公共库，提供给第三方的API，要使用interface

## 参考
1. [语言参考文档](https://www.tslang.cn/docs/home.html)
2. [Interface vs Type alias in TypeScript 2.7 – Martin Hochel – Medium](https://medium.com/@martin_hotell/interface-vs-type-alias-in-typescript-2-7-2a8f1777af4c)