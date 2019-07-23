---
title: TypeScript学习6-函数
date: 2019-07-23 18:30:49
tags:
 - TypeScript
 - JavaScript
categories: TypeScript
---
## 引言
TypeScript中的函数和JavaScript中的函数，和其他特性相比，是相差不大的。  
这里会补充一点进阶知识。

## 函数相关的知识点

下面列举一些TypeScript中函数相关的知识点。

### 类型声明

TypeScript中的函数定义，需要声明参数和返回值的类型。
```typescript
// function add
function add(x: number, y: number): number {
    return x + y;
}
// arrow funtion add
const add = (x: number, y: number) => x + y;
```
上面是两种基本用法：普通函数定义、箭头函数定义。  
其中隐藏有一个点：**类型推断**，箭头函数没有声明返回值类型，这里编译器不会报错，因为它可以推断出返回值类型。这个特性，在数据类型学习的时候也接触过，规则也是类似的：**能推断出类型的，不需要显示声明**。

### 可选参数和参数默认值

这两个特性实际使用的时候是很有用的，很清晰，也省掉不少代码。
```typescript
// function log
function myLog(message: string,  level: ('info' | 'warn' | 'error') = 'info', prefix?: string): void {
  // todo, logging
}
```
这里：
- level参数是有一个默认值的
- prefix参数是可选的  
注意一点：**可选参数必须放最后**

### 剩余参数

剩余参数就是，剩余的其他参数，这里用到了结构，看示例。
```typescript
function myCallback(status: boolean, ...restData: any[]) {
  console.log(`${status}: ${restData.join()}`);
}

myCallback(false);
myCallback(true, '完成一个');
myCallback(true, '完成第一个', '完成第二个');
```
剩余参数使用起来非常灵活。

## 进阶知识点

### this指向

JavaScript中的this是个入门的难点，JavaScript函数的this是和调用上下文直接相关的，和定义不相关，所以很容易出现this指向和预期不一致的问题。  

目前有几种解决方案：this绑定、箭头函数、闭包。  

#### 箭头函数

箭头函数的出现，可以缓解了这个难题。箭头函数中的this，是和定义的上下文相关（**底层是利用闭包实现的**），如下例：

```typescript
const myLottery = {
  prizes: ['pen', 'pencil', 'notebook', 'dictionary'],
  getDraw: function() {
    return (name: string) => {
      const result = Math.floor(Math.random() * 4);
      return `Bingo, ${name}, you win the ${this.prizes[result]}!`;
    }
  }
}

const myDraw = myLottery.getDraw();
console.log(myDraw('Tom'));
```
#### 闭包实现

闭包方案就是在函数上下文中把this保存在变量中。

```typescript
  ...
  getDraw: function() {
    const _this = this;
    return function (name: string) {
      const result = Math.floor(Math.random() * 4);
      return `Bingo, ${name}, you win the ${_this.prizes[result]}!`;
    }
  }
  ...
```

#### bind绑定

bind绑定在TypeScript里面会麻烦一点，需要声明this类型，否则编译不过，提示this类型不确定。

```typescript
interface Lottery {
  prizes: string[];
  getDraw(): {(name: string): string};
}

const myLottery: Lottery = {
  prizes: ['pen', 'pencil', 'notebook', 'dictionary'],
  getDraw: function() {
    return function (this: Lottery, name: string) {
      const result = Math.floor(Math.random() * 4);
      return `Bingo, ${name}, you win the ${this.prizes[result]}!`;
    }.bind(this);
  }
}

const myDraw = myLottery.getDraw();
console.log(myDraw('Tom'));
```

### 函数重载

TypeScript的重载，我是感觉有点牵强的，如下示例：

```typescript
function myAdd(x: number, y: number): number;
function myAdd(x: string, y: string): string;
function myAdd(x, y): any {
  if (typeof x === 'number' && typeof y === 'number') {
    return x + y;
  } else if (typeof x === 'string' && typeof y === 'string') {
    return x + y;
  }
}

myAdd(1, 2);
myAdd('Hello', 'world');
```
这种实现，其实就是动态参数的分支处理，好处是代码提示比较友好，能识别出来重载。

>无法实现类似静态语言的重载的原因之前也提过：静态语言，如Java，函数重载，是生成了多个不同名的函数，同时使用的地方也会被更新成对应的函数名。TypeScript要兼容JavaScript，这个就无法实现。

### 高阶函数

高阶函数听起来很高大上，其实概念并不难：**参数是函数或返回值是函数的函数**。  

我们接触比较多的比如，数组内置的map方法。

```typescript
// 数组元素自增
let arr  = [1, 2, 3];

// 传统方式
for(let i=0; i < arr.length; i++) {
    arr[i]++;
}

// map
arr = arr.map(function(item) {
    return item + 1;
});

// 结合箭头函数
arr = arr.map(item => item + 1);
```

下面再学一个复杂一点的案例，前置处理器。比如，我们要在请求数据之前，记录一些日志。

```typescript
interface Request {
  (url: string, options: {[key: string]: any}): Promise<any>;
}

function withLog(func: Request): Request {
  return function(url: string, options: {[key: string]: any}) {
    console.log('request');
    return func(url, options);
  };
}

function myRequest(url: string, options: {[key: string]: any}) {
  return new Promise(function(resolve) {
      setTimeout(() => {
        resolve('completed!')   
      }, 1000);
  });
}

withLog(myRequest)('/a', {method: 'post'}).then(val => {
  console.log(val);
})
```
上述方案就是把请求函数通过另外一个函数包装成一个新的函数。

再复杂一点，像这样，参数是函数，返回值也是函数的，是可以无限嵌套的，每嵌套一次，它就多了一个功能。这在很多框架里面特别常见，用于抽象和解耦。

```typescript
function withLog2(func: Request): Request {
  return function(url: string, options: {[key: string]: any}) {
    return func(url, options).then(val => {
      return `[${val}]`;
    });
  };
}

withLog2(withLog(myRequest))('/a', {method: 'post'}).then(val => {
  console.log(val);
})
```

