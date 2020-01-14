---
title: TypeScript 3.7 - 断言函数
date: 2019-12-01 20:09:03
tags:
 - TypeScript
 - JavaScript
categories: TypeScript
---
## 断言函数

[试试看](https://www.typescriptlang.org/play/#example/assertion-functions)

有一类特定的函数，在非预期结果出现时会抛出一个错误。
这类函数就叫做断言函数。
例如，Node.js 有一个专用的断言函数叫 `assert`。

```js
assert(someValue === 42);
```

在这个示例中，如果 `someValue` 不等于 `42`，那么 `assert` 就会抛出一个 `AssertionError`。

JavaScript 中的断言经常用于确保传入的是正确的类型。
比如，

```js
function multiply(x, y) {
  assert(typeof x === 'number');
  assert(typeof y === 'number');

  return x * y;
}
```

不幸的是，TypeScript 中的这些检查从来不会被正确的编写。
对于弱类型的写法，意味着 TypeScript 不会严格检查，而对于稍微规范一些的写法，一般要求使用者添加类型断言。

```ts
function yell(str) {
  assert(typeof str === 'string');

  return str.toUppercase();
  // 哎呀！我们错误地拼写了 'toUpperCase'
  // 如果 TypeScript 依然能捕获问题，那就太棒了！
}
```

这里有可供选择的替代写法，可以让编程语言分析出问题，不过并不方便。

```ts
function yell(str) {
  if (typeof str !== 'string') {
    throw new TypeError('str should have been a string.');
  }
  // 错误被捕获！
  return str.toUppercase();
}
```

TypeScript 最基本的目标就是用最友好的方式去对现有的 JavaScript 结构做类型分析。
基于这个原因，TypeScript 3.7 引入了一个新的概念叫“断言签名(assertion signatures)”，用来模拟这些断言函数。

第一种断言签名，模拟 Node 中的 `assert` 函数的功能。
它确保在断言的范围内，断言条件必须为 `true`。

```ts
function assert(condition: any, msg?: string): asserts condition {
    if (!condition) {
        throw new AssertionError(msg)
    }
}
```

`断言条件` 说的是，如果 `assert` 返回了，`condition` 参数得到的传入值必须为 `true`，因为如果不是这样，它肯定会抛出一个错误。 
这意味着，在断言的范围内，这个条件一定是正确的。
举一个例子，用这个断言函数意味着我们可以实现捕获我们之前的 `yell` 示例的错误。

```ts
function yell(str) {
    assert(typeof str === "string");

    return str.toUppercase();
    //         ~~~~~~~~~~~
    // error: Property 'toUppercase' does not exist on type 'string'.
    //        Did you mean 'toUpperCase'?
}

function assert(condition: any, msg?: string): asserts condition {
    if (!condition) {
        throw new AssertionError(msg)
    }
}
```

另外一种断言签名不是用来校验一个条件，而是告诉 TypeScript 某个变量或属性有不同的类型。

```ts
function assertIsString(val: any): asserts val is string {
    if (typeof val !== "string") {
        throw new AssertionError("Not a string!");
    }
}
```

这里 `asserts val is string` 保证在 `assertIsString` 调用之后, 任何传入的变量将被认为是一个 `string`.

```ts
function yell(str: any) {
  assertIsString(str);

  // 现在 TypeScript 知道了，'str' 是一个 'string'。

  return str.toUppercase();
  //         ~~~~~~~~~~~
  // error: Property 'toUppercase' does not exist on type 'string'.
  //        Did you mean 'toUpperCase'?
}
```

这里的断言签名非常类似于类型断言签名：

```ts
function isString(val: any): val is string {
  return typeof val === 'string';
}

function yell(str: any) {
  if (isString(str)) {
    return str.toUppercase();
  }
  throw 'Oops!';
}
```

就像类型断言签名一样，这些断言签名是非常神奇的。
我们可以用它们实现一些非常复杂的想法和设计。

```ts
function assertIsDefined<T>(val: T): asserts val is NonNullable<T> {
    if (val === undefined || val === null) {
        throw new AssertionError(
            `Expected 'val' to be defined, but received ${val}`
        );
    }
}
```

想阅读更多断言签名相关内容， [签出原始的 pull request](https://github.com/microsoft/TypeScript/pull/32695).