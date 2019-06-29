---
title: TypeScript学习4-类
date: 2019-06-29 21:59:48
tags:
 - TypeScript
 - 接口
 - JavaScript
categories: TypeScript
---
## 类-面向对象的基础
面向对象编程，应该是目前使用最广泛的编程模式。  

JavaScript是基于原型的语言，自从广泛使用之后，面向对象的改造一直没停过，不少第三方库都使用原型实现了JavaScript下面的类，如：MooTools，Backbone。再到ES6标准，直接原生支持了类定义，不过只能算语法糖级的支持。

TypeScript对面向对象支持的更彻底，非常接近Java、C#语言，支持接口、类、抽象、继承、可见性等面向对象特性。

## 面向对象三大特征

### 抽象
即同一类事物，我们可以把它抽象成一个描述，就是类。对外，我们看到的就是这个类描述，它有什么属性，它能干嘛，取决于它对外公开的属性和方法。它的内部如何运作，我们无需关注。  

比如我们要用面向对象语言描述动物：动物有一个名字，动物能移动。它怎么做到能移动呢，外部是不用关心的。

### 继承
如果抽象是基础，那么继承就是面向对象的高峰。抽象给了编程语言描述世界的画笔，那么继承就给了它色彩。  

继承类拥有父类公开的、保护的属性和方法，同时还可以添加自己的属性和方法。

**一般分为实现继承、接口继承。**

再拿动物来说，鸟类是动物的一种，它不光继承了动物的特性，它还有自己的专属特性：它有翅膀，它能飞上天空。

### 多态
多态简单来说，就是同一个类的对象，表现出不同的特性，是继承延伸出来的表现结果。

**常见多态有覆盖、重载两类。**
> TypeScript中的类方法不支持真正意义上的重载，可以通过增加可选参数来达到重载的目的。  
具体的原因，是因为重载在编译之后，是以多个不同名的函数存在的，调用哪个也是编译的时候确定的。而TypeScript需要支持JavaScript来调用它，JavaScript不知道编译之后的函数名。  
后面会拿一篇文章来分析。

还是拿动物为例，继承并覆盖move方法之后，鸟类实现了飞、鱼类实现了游。

## TypeScript中类使用的常见场景

- 使用TypeScript描述类。
```javascript
// 知识点：抽象类
abstract class Animal {
    // 知识点：私有成员
    private _name: string;

    // 知识点：存取器
    get name(): string {
      return this._name;
    }
  
    constructor(name: string) {
      this._name = name;
    }
  
    // 知识点：抽象方法
    abstract move(distance: number): void;
}
```

- 描述子类
```javascript
// 知识点：实现抽象类的子类
class Bird extends Animal {
  // 知识点：只读成员
  // 知识点：静态成员
  static readonly classification = 'bird';
  
  // 知识点：super()
  constructor(name: string) {
      super(name);
  }
  
  // 知识点：抽象方法实现
  move(distance: number): void {
      console.log(`moved ${distance}.`)
  }
}

const enum MoveMode {
  WALK,
  FLY
}

// 知识点：子类
class Owl extends Bird {

  private _mode: MoveMode;
  
  get mode(): MoveMode {
      console.log('read move mode.');
      return this._mode;
  }
  
  set mode(mode: MoveMode) {
      console.log('changed move mode.');
      this._mode = mode;
  }
  
  // 知识点：super()
  constructor(name: string) {
      super(name);
      this._mode = MoveMode.FLY;
  }
  
  // 知识点：方法覆盖
  move(distance: number): void {
    switch (this._mode) {
      case MoveMode.FLY:
          console.log(`fly ${distance}.`);
          break;
      case MoveMode.WALK:
          console.log(`walked ${distance}.`);
          break;
      default:;
    }
  }

}
```

## 类相关的知识点总结
从上述示例中，整理出以下知识点，请结合示例回顾，加强一下理解。

### 抽象类、抽象方法  
抽象类不能实例化，抽象方法不能实现，需要在子类中实现。

### 抽象类实现
继承抽象类，必须实现抽象方法。

### 子类
继承了父类的类，是相对的。

### 类成员可见性
- public，公开，默认的可见性
- protected，保护，子类可见
- private，私有，本类可见

### 静态成员
静态成员属于类。

### 只读成员
只读，即初始化后不可修改，只能在声明和构造函数里初始化。

### 类成员存取器
可以像成员一样来访问，一般用来做一些处理，比如校验，格式化等。

### 类方法覆盖
覆盖父类方法，实现自己的特性。
