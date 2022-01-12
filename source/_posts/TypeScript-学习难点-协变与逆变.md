---
title: TypeScript 学习难点 - 协变与逆变
date: 2022-01-12 11:51:52
tags:
 - TypeScript
 - JavaScript
categories: TypeScript
---
# TypeScript 学习难点 - 协变与逆变

在父子类型的互相赋值时，类型是否安全呢？比如，我们声明两个父子类型：

```typescript
interface Animal {
  name: string;
}

interface Dog extends Animal {
  wangwang(): void;
}

let animal1: Animal;
let dog1: Dog;
animal1 = dog1;
```
`dog1` 赋值给 `animal1` 类型是否安全？如果学过面向对象编程就会有印象：子类型可以赋值给父类型，子类型拥有父类型所有的属性和方法，完全可以当父类型使用。

下面这种情况呢？类比子类型可以赋值父类型，`Dog 数组`明显是可以赋值给`Animal 数组`的，说明它们还具有父子关系。

```typescript
let arrAnimal: Array<Animal>;
let arrDog: Array<Dog>;
arrAnimal = arrDog;
```

这种父子类型构造的新类型的父子关系？这个就是 `协变与逆变`，维基百科定义如下：

> 协变与逆变(Covariance and contravariance )是在计算机科学中，描述具有父/子型别关系的多个型别通过型别构造器、构造出的多个复杂型别之间是否有父/子型别关系的用语。

概念有点难以理解，我们通俗一点讲：`具有父子关系的多个类型，通过某种构造关系构造成的新类型，如果还具有父子关系则是协变的，而关系逆转了就是逆变的`.

通过这个概念，我们可以知道上面的示例是`协变`.

我们再看一个`逆变`的示例：

```typescript
type AnimalFn = (arg: Animal) => void
type DogFn = (arg: Dog) => void

let animalFn: AnimalFn;
let dogFn: DogFn;

// 只能这样赋值，也就是放在函数参数上，函数之间的关系逆变了
dogFn = animalFn;
```

我们可以这样理解：不同类型赋值时，目标类型的功能范围要小于原始类型：**你无法做你能力范围之外的事情**。

对象赋值时，子对象赋值给父对象(这里的赋值是引用的赋值)，使用时是把子对象当做父对象操作，功能范围缩小了，所以是安全的。这是协变。

函数赋值时，父子类型放在函数参数上时，如果你把子类型函数赋值给父类型函数，使用时参数类型是父类型，但是函数内部操作是当子类型来操作的，相当于把父类型当子类型来操作了，这是功能范围放大了，所以这样不安全，反过来就是安全的。这是逆变。

另外，函数赋值时，如果父子类型放在返回值上时，它们又是协变的，根据上面的说明类比分析一下：函数返回的子类型当然可以作为父类型来操作。