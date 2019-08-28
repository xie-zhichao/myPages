---
title: TypeScript学习7：泛型
date: 2019-08-28 15:59:08
tags:
 - TypeScript
 - JavaScript
categories: TypeScript
---
## 为什么引入泛型
如果说接口和类是对一类事物的抽象描述，那么泛型可以说是对一类数据的抽象描述，进一步加强了语言的抽象程度，让组件重用性更好。  

看一个小例子，实现数组的单条数据插入：

```typescript
function insertTo<T>(array: T[], index: number, t: T): T[] {
  if (index < 0 || array.length - 1 < index) {
    throw new Error('Illegal index.');
  }
  array.splice(index, 0, t);
  return array;
}

const arr1 = [1, 2, 4];
insertTo(arr1, 2, 3);
console.log(arr1);

const arr2 = ['a', 'b', 'd'];
insertTo(arr2, 2, 'c');
console.log(arr2);
```
这个函数使用了泛型来表示传入参数的类型，我们可以很明显感觉到泛型的好处：一个函数就可以适配多种类型。用普通方式，我们要重复这段逻辑写n个函数，用泛型一个就够用，这就是抽象的好处。

## 泛型的不同场景
泛型可以用于函数，也可以用于类，还有更高级的用法：带限定的泛型。后面高级类型，还会用到泛型，会更复杂一些。

### 泛型函数
上面已给了一个例子，这里再补充一个用法，限定类型的泛型函数：

```typescript
interface GenericInsertFn<T> {
    <T>(array: T[], index: number, t: T): T[]
}

let myInsertTo: GenericInsertFn<number> = insertTo;
```

通过类型限定之后，我们就得到了一个新的函数，这个函数只能操作数字类型的数组。

### 泛型类
泛型类和泛型函数用法类似，只是泛型声明在类上，整个类范围都可以用。  
看个小例子：

```typescript
class MyArrayList<T> {
  private _array: T[];

  constructor() {
    this._array = [];
  }

  get lenght(): number {
    return this._array.length;
  }

  get(index: number): T {
    if (index < 0 || this._array.length - 1 < index) {
      throw new Error('Illegal index.');
    }
    return this._array[index];
  }

  push(...elements: T[]): number {
    return this._array.push(...elements);
  }

  insertTo(index: number, t: T): T[] {
    if (index < 0 || this._array.length - 1 < index) {
      throw new Error('Illegal index.');
    }
    this._array.splice(index, 0, t);
    return this._array;
  }
}
```

以上例子实现了一个简单的泛型的ArrayList，如果你熟悉Java语言，对此就不陌生了。实现的效果和函数类似，也是抽象和重用。

### 泛型的限定
有的场景，泛型需要限定范围，比如一个任务执行者函数，需要接受实现了工作者接口的函数作为参数才可以执行，那么就需要带限定的泛型参数，看代码：

```typescript
interface IWorker {
  (task: string): number; 
}

function excutor<W extends IWorker>(r: W) {
  r('case1');
}

const myWorker: IWorker = function (task: string) {
  console.log(`${task} is start.`);
  return 1;
}

excutor(myWorker);
```

再看一个更复杂一点的用法，多重泛型，抽象度也更高，通用性也更好。

```typescript
interface IJobRunner<T, K> {
  run(t: T): K;
}

interface ITimeJobExcutor<T, K, R extends IJobRunner<T, K>> {
  excute(r: R): void;
}
```
基本的泛型用法学习完了，后面高级类型还会继续学习更高级的用法。
