---
title: TypeScript学习3-接口
date: 2019-06-04 18:26:40
tags:
 - TypeScript
 - 接口
 - JavaScript
categories: TypeScript
---
## 接口
### 什么是接口
TypeScript的核心就是类型检查，接口就是用于声明类型，给内部或第三方使用者提供类型声明和约束。

### 使用场景
- 数据类型声明和约束
```typescript
// 声明数据类型
interface CustomerInfo {
  customerCode: string;
  customerName: string;
}

// 使用数据类型
function helloCustomer(customerInfo: CustomerInfo) {
  console.log(`Hello, ${customerInfo.customerName}!`);
}

helloCustomer({customerCode: '1001', customerName: 'Jee'}); // ok
helloCustomer({customerName: 'Jee'}); // error, Property 'customerCode' is missing
```

- 面向对象编程

这里和面向对象语言类似，用于定义对象接口，声明对象的结构，定义类时可以实现接口，满足这个接口定义的功能。
```typescript
// 接口声明
interface Animal {
  name: string;
}

// 接口继承
interface Mammal extends Animal {
  legNum: number;
  sound(): string;
}

// 类实现
class Bull implements Mammal {
  name: string;
  legNum: number;
  
  constructor(name: string) {
    this.name = name;
    this.legNum = 4;
  }

  sound() {
    return 'moo';
  }
}

// 实例
let bull1 = new Bull('bull1');
console.log(bull1.sound()); // moo
```

### 可变接口属性

有两种方式可以实现可变属性，属性明确时，推荐第一种。不明确时可以用第二种。

- 可选属性，定义可以选传的属性
```typescript
interface CustomerInfo {
  customerCode: string;
  customerName: string;
  customerAddr?: string; // 可选属性
}
```
- 索引类型，支持string，number索引属性
```typescript
interface CustomerInfo {
  customerCode: string;
  customerName: string;
  customerAddr?: string; // 可选属性
  [key: string]: any; // 其他任意名称，任意类型的属性
}
```
> 这里说明一下，因为JavaScript对象中，数字索引是会转换成string来取值的。如果一个接口里面，同时有number、string索引属性时，number索引属性的类型，必须时string索引属性的子类型。**也就是，number索引的属性类型，必须能被string索引的属性类型覆盖；用number索引去取值，取到的值也是string索引属性的类型。**

### 只读属性
- 只读属性，初始化后不能修改，readonly修饰
```typescript
interface CustomerInfo {
  greeting: string; // 普通属性
  readonly standardGreeting: string; // 只读属性
  customerCode: string;
  customerName: string; 
}
```
> const vs readonly，变量用const，属性用readonly

### 函数类型
接口除了能描述对象类型，还能描述函数类型（这个和面向对象语言有点不一样，需要理解一下）。
```typescript
interface SearchFunc {
  (content: string, key: string): boolean;
}

// 这里参数名可以不一样，类型也可以省略
let mySearchFunc: SearchFunc = (c, k) => {
  return c.search(k) > -1;
}
```

### 后续
会再写一遍类的学习，一篇接口与类的结合使用。
