---
title: TypeScript学习1-TS出现的背景和简单介绍
date: 2019-05-07 17:49:25
tags:
 - TypeScript
 - JavaScript
categories: TypeScript
---
## TypeScript出现的背景

前端项目趋向工程化之后，**模块化**开发模式开始成为主流：

公用的逻辑和UI都被抽出来，独立成模块，供外部调用。在调用这些模块时，就会面临着沟通问题，我们要知道如何去调用，参数是什么，返回值是什么，它们的类型和结构？

### 传统的做法

- 第三方库，如JQuery，你想通过看源文件来查看如何调用，难度很大，一般都是压缩混淆过的。只能访问技术文档网站，查API文档。
- 内部的库，有写文档最好，可以读文档。没有写文档的，只能去读源代码，或当面去问。

### 存在的问题
- 第三方库，文档一般都是维护较好的，多花一点时间去读文档就ok了
- 内部库，如果文档维护不好，可能会出现用法不对，出现问题，影响开发质量和进度

## 什么是TypeScript
TypeScript是JavaScript的超集，在它的基础上做了一层封装，提供了静态类型，高级数据类型等语言特性。最终还是编译成JavaScript执行。

### 静态类型
静态类型是TypeScript的核心特性。
先通过示例来看看JavaScript和TypeScript的对比。

```javascript
// JavaScript
export function formatA(var1) {
    let ret;
    if(typeof var1 === 'string') {
        ret = '[log]' + var1;
    } else {
        throw new Error('param must be a string!');
    }
    
    return ret;
}
```

```typescript
// TypeScript
export function formatA(var1: string): string {
    return '[log]' + var1;
}
```

更复杂一点的例子
```javascript
// JavaScript
export const fetch = function (url, params, user) {
  // dosomething

  return http(options).then(data => {
    return data
  }).catch(err => {
    return err
  })
}
```

```typescript
// TypeScript

export interface User {
    userid: number,
    username: string,
    userTags?: Array<string>
}

export const fetch = function (url: string | object, params?: any, user?: User): Promise<object | Error> {
  // dosomething

  return http(options).then(data => {
    return data
  }).catch(err => {
    return err
  })
}
```

对比下来，感受很明显：
- TypeScript参数和返回值类型明确，调用时不会用错
- TypeScript中的数据结构也是明确的，不需要查文档也能确定如何使用

**TypeScript让代码描述自身**

## IDE
- VSCode对TypeScript支持也很友好，能达到强类型语言，如Java的语法提示体验。
