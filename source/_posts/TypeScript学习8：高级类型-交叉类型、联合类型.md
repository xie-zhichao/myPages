---
title: TypeScript学习8：高级类型-交叉类型、联合类型
date: 2019-10-11 11:32:15
tags:
 - TypeScript
 - JavaScript
categories: TypeScript
---
## 简介
此为高级类型的第一部分，学习交叉类型(Intersection Types)、联合类型(Union Types)以及在使用以上类型时如何区分具体类型。

## 交叉类型(Intersection Types)
交叉类型把几个类型的成员合并，形成一个拥有这几个类型所有成员的新类型。从字面上理解，可能会误认为是把取出几个类型交叉的（即交集）成员。

交叉类型的使用场景：Mixins、不适合用类来定义。

>我感觉，交叉类型和Mixins有一点区别：交叉类型只是一个类型声明，用于类型约束；Mixins会给类增加成员，new对象时，对象会包含增加的成员属性。

我们看一看示例：

>改自官方示例，官方示例有2个小问题：会提示不存在属性“hasOwnProperty”；另外，**es6下prototype是不可以枚举的，无法通过枚举合并类属性。**

```javascript
  interface IAnyObject {
    [prop: string]: any
  }

  function extend<First extends IAnyObject, Second extends IAnyObject>(first: First, second: Second): First & Second {
    const result: Partial<First & Second> = {};
    for (const prop in first) {
      if (first.hasOwnProperty(prop)) {
        (result as First)[prop] = first[prop];
      }
    }
    for (const prop in second) {
      if (second.hasOwnProperty(prop)) {
        (result as Second)[prop] = second[prop];
      }
    }
    return result as First & Second;
  }

  interface IPerson {
    name: string,
    age: number
  }

  interface IOrdered {
    serialNo: number,
    getSerialNo(): number
  }

  const personA: IPerson = {
    name: 'Jim',
    age: 20
  }

  const orderOne: IOrdered = {
    serialNo: 1,
    getSerialNo: function () { return this.serialNo }
  }

  const personAOrdered = extend<IPerson, IOrdered>(personA, orderOne);
  console.log(personAOrdered.getSerialNo());
```

- 其中**First & Second**就是交叉类型的写法。
- 通过扩展，可以合并两传入对象的成员属性，增强现有对象的能力。
 
## 联合类型(Union Types)
联合类型与交叉类型类似，都可以拥有多个类型的能力，区别是：**联合类型一次只能一种类型；而交叉类型每次都是多个类型的合并类型。**

联合类型在实际项目中，使用场景比交叉类型广泛得多。

1. 字符串填充：

```javascript
  function padLeft(value: string, padding: number | string) {
    if (typeof padding === "number") {
      return Array(padding + 1).join(" ") + value;
    }
    if (typeof padding === "string") {
      return padding + value;
    }
    return value;
  }

  padLeft("Hello world", 4);
```

2. 再举个常见的例子，常量的联合类型，五分制考试打分：

```javascript
  type Scores = 1 | 2 | 3 | 4 | 5;

  function rating(score: Scores) {
    console.log(`Set score ${score}`);
  }

  rating(3);
  // error
  // rating(6); 
```

枚举也可以实现，但是这样更简洁。

3. 还可以用于可为null的参数：
```javascript
function f(sn: string | null): string {
    if (typeof sn === null) {
        return 'default';
    } else {
        return sn;
    }
}
```

4. 还可以使用类型别名定义联合类型(上面场景2中用过)：
```javascript
type Method = 'get' | 'post';
```

## 类型保护和类型区分
使用联合类型时，我们是无法知道编译时的具体类型的，所以在运行时必须要确定类型。所以代码中需要做类型保护和类型区分。

为什么叫类型保护？因为在运行时，必须知道明确类型，否则可能会访问一个不存在的成员属性，导致程序出错。

- 自定义类型保护
    ```javascript
      // 先判断是否存在成员属性
      if(pet.swim) {
          
      }
    ```
- in操作符
    ```javascript
      // 先判断是否存在成员属性
      if('swim' in pet) {
          
      }
    ```
- typeof操作符
    ```javascript
    function f(sn: string | number): string {
        if (typeof sn === 'number') {
            return sn.toString();
        } else {
            return sn;
        }
    }
    ```
- instanceof操作符
    ```javascript
    // 判断实例原型
    if(pet instanceof Fish) {
          
    }
    ```
- 类型断言
    ```javascript
    function f(sn: string | null): string {
        if (sn === null) {
            return "default";
        } else {
            return sn;
        }
    }
    ```
    
联合类型应用极广泛，可以多实践。