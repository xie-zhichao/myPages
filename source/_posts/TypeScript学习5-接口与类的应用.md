---
title: TypeScript学习5-接口与类的应用
date: 2019-07-18 15:28:41
tags:
 - TypeScript
 - JavaScript
categories: TypeScript
---
## 概述和对比

接口和类在实际使用中，一般都是配合使用的。我们来对比一下：
- **接口**可以声明一个类的结构，包含属性和方法，但它只是给出一个声明，没有访问修饰符，没有具体的方法实现，没有构造函数，也不可以被实例化。
- **接口**是一个对外的协商、约定。继承后才可以实例化，继承的时候必须实现声明。可以多重继承。
- **类**是也是声明一个类的结构，包含属性和方法，有访问修饰符，有具体的方法实现，有构造函数，可以实例化（**抽象类下面说**）。继承的时候，可以覆盖，也可以继承使用父类实现。
- **抽象类**也是类，不过它可以包含接口类似的特性：普通方法包含实现，抽象方法是不包含实现的；抽象类不能实例化。继承后才可以实例化，继承的时候必须实现抽象声明，其他声明和普通类一样。

> 补充说明一下，这里的接口、类与纯正的面向对象语言Java比，有一些不同的地方，有兴趣的可以自行了解。

## 接口与类的使用场景
这里将给一个稍微复杂一点的例子：旅行社运送旅客。  

### 定义接口

这里我们定义了几类接口：旅客、载客、位置、运送，目的就是声明规范，实现了这些接口的类就可以被用来做这些事情。
```javascript
// 旅客
interface Passgener {
	name: string;
}

// 载客
interface Carriable {
  passengers: Passgener[];
  getUp (...passengers: Passgener[]): number;
  getOff (): number;
}

// 位置
interface Positioned {
	x: number;
	y: number;
}

// 常量：初始位置
const positionOrigin: Positioned = { x: 0, y: 0 };

// 运送
interface Moveable {
	position: Positioned;
	moveTo(Positioned: Positioned): Positioned;
}
```

## 实现具体类

这里我实现了两个类：小汽车、巴士，这两个类均实现了载客、运送接口，也就是说明它们是拥有这些能力的。

```javascript
// 小汽车
class Car implements Carriable, Moveable {
  passengers: Passgener[];
  position: Positioned;
  capacity: number;

  constructor(capacity: number) {
	this.passengers = [];
	this.position = positionOrigin;
	this.capacity = capacity;
  }
  
  getUp (...passengers: Passgener[]): number {
    if (passengers.length > this.capacity) {
      throw new Error(`This car can carry ${this.capacity} passengers!`);
    }
    console.log(
			`This car carries ${passengers.map(item => item.name).join(',')}`
		);
    return this.passengers.push(...passengers);
  }

  getOff (): number {
    return this.passengers.splice(0).length;
  }

	moveTo(position: Positioned): Positioned {
		Object.assign(this.position, position);
		console.log(
			`This car carries ${this.passengers.map(item => item.name).join(',')} to [${
				this.position.x
			}, ${this.position.y}]`
		);
		return this.position;
	}
}

// 巴士
class Bus implements Carriable, Moveable {
  passengers: Passgener[];
  position: Positioned;
  capacity: number;

  constructor(capacity: number) {
	this.passengers = [];
	this.position = positionOrigin;
	this.capacity = capacity;
  }
  
  getUp (...passengers: Passgener[]): number {
    if (passengers.length > this.capacity) {
      throw new Error(`This Bus can carry ${this.capacity} passengers!`);
    }
    console.log(
			`This Bus carries ${passengers.map(item => item.name).join(',')}`
		);
    return this.passengers.push(...passengers);
  }

  getOff (): number {
    return this.passengers.splice(0).length;
  }

	moveTo(position: Positioned): Positioned {
    Object.assign(this.position, position);
    console.log(
			`This Bus carries ${this.passengers.map(item => item.name).join(',')} to [${
				this.position.x
			}, ${this.position.y}]`
		);
		return this.position;
	}
}
```

## 实现功能

具体类定义出来了，我们就可以实现功能，我们定义一个旅行社，由旅行社来运送旅客。
```javascript
// 旅行社
class TravelAgency {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  carrying (carrier: Carriable & Moveable, position: Positioned, ...passengers: Passgener[]): Positioned {
    carrier.getUp(...passengers);
    return carrier.moveTo(position);
  }

}

// 实例化对象
let t1 = new TravelAgency("t1");
let c1 = new Car(4);
let b1 = new Bus(30);

// 实现功能
t1.carrying(c1, {x: 10, y: 60}, {name: 'Jack'}, {name: 'Tom'});
t1.carrying(b1, {x: 10, y: 60}, {name: 'Jack'}, {name: 'Tom'}, {name: 'Mary'}, {name: 'Joe'});
```

## 抽象类的使用场景

上面已经把完整的功能实现出来了，但是我们可以看到，Car和Bus的代码还是有冗余的。这里我们就可以通过抽象类来做一些优化。

我们先实现一个交通工具的抽象类，然后继承实现轮船类。我们可以发现抽象类已经实现的方法，子类是无需重复实现的。只有抽象出来的必须子类实现的才需要实现。

```javascript
// 交通工具
abstract class Vehicle implements Carriable, Moveable {
  name: string;
  passengers: Passgener[];
	position: Positioned;
  capacity: number;
  
  // 抽象方法
  abstract run(speed: number): void;
  
  getUp (...passengers: Passgener[]): number {
    if (passengers.length > this.capacity) {
      throw new Error(`This ${this.name} can carry ${this.capacity} passengers!`);
    }
    console.log(
			`This ${this.name} carries ${passengers.map(item => item.name).join(',')}`
		);
    return this.passengers.push(...passengers);
  }

  getOff (): number {
    return this.passengers.splice(0).length;
  }

	moveTo(position: Positioned): Positioned {
		Object.assign(this.position, position);
		console.log(
			`This ${this.name} carries ${this.passengers.map(item => item.name).join(',')} to [${
				this.position.x
			}, ${this.position.y}]`
		);
		return this.position;
	}
}

// 轮船
class Ship extends Vehicle {
  constructor(capacity: number) {
    super();
		this.passengers = [];
		this.position = positionOrigin;
		this.capacity = capacity;
  }

  // 抽象方法必须实现 
  run(speed: number) {
    // todo, run as a ship...
    console.log(`This ${this.name} running at ${speed}km/h.`);
  }
}
```

到此介绍完了接口和类的基本使用场景，再次体会一下关键字：**抽象、继承**。
