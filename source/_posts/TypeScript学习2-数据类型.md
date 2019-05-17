---
title: TypeScript学习2-数据类型
date: 2019-05-17 16:55:16
tags:
 - TypeScript
 - JavaScript
categories: TypeScript
---
## 变量类型
TypeScript支持的变量类型与JavaScript基本一样。另外提供了**枚举类型**。

这里还是给出一些例子去理解TypeScript的变量类型使用，具体的语言知识可以查看文档。
### 原始类型
```javascript
// boolean
let success: boolean = true;
// number
let num1: number = 12;
let num2: number = 0xab; // 支持多种进制
// string
let str1: string = 'abc';
```
除上述示例，还有null, undefined, symbol类型，可以参看文档。

### 数组和元组
数组有两种声明方式：
```javascript
let arr1: string[] = ['hello', 'world'];
let arr2: Array<string> = ['hello', 'world'];
```
元组是一种特殊的数组
```javascript
let arr1: ['string', 'number'] = ['hello', 123];
```
> 元组越界时，会以联合类型来处理，具体请查阅文档。

### 枚举
枚举类型与C#，Java语言相比，另外提供了一个便利特性，可以拿到枚举的名称。
```javascript
enum Color {Red = 1, Green, Blue}
let colorName: string = Color[2];

console.log(colorName);  // 显示'Green'因为上面代码里它的值是2
```
### any, void
- any表示任意类型，可以在类型不确定时使用（**能不用就不要用**），如：
    - 第三方的返回值，你无法确定类型
    - 你不确定你将会用到哪些类型
- void正好相反，表示没有类型，一般用于没有返回值的函数

### null, undefined
- 类型定义和JavaScript一致
- TypeScript里面，可以赋给：本身类型、any、void

### object
object类型是基本的6个类型之外的其他类型的基类。

使用时需注意，用object声明时，后面只能当object使用，如：
```javascript
let a: object = new Date();
a.hasOwnProperty('name'); // ok
a.getDate(); // error
```

### never
never是个比较特殊的类型，表示永远不会到达。

典型的场景就是异常抛出函数的返回值。
```javascript
function bizError(code: number, msg: string): never {
   throw new Error({
       code,
       msg
   }); 
}
```

## 类型断言
类型断言，我觉得也可以理解为强制类型转换。

这个特性和强类型语言类型，看几个例子理解一下：
```javascript
// 尖括号式
let var1: any = 'abc';
console.log((<string>var1).substring(1));

// as
let var2: object = new Array<number>();
console.log((var2 as Array<number>).push(1));
```

## 类型推断
有些情况下，不需要指明变量类型，TypeScript可以根据上下文自动推断类型。如下示例：
```javascript
let a = 'abc';
console.log(a.substring(1));

let obj1 = {
    str1: 'abc',
    num1: 123
};

let { str1, num1 } = obj1;
```

## 参考
1. TypeScript语言手册 https://www.tslang.cn/docs/handbook/basic-types.html