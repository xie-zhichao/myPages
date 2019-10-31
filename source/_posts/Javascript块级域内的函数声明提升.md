---
title: Javascript块级域内的函数声明提升
date: 2019-10-31 17:20:25
tags:
  - JavaScript
  - 变量声明提升
  - 作用域
categories: JavaScript
---
## 主题
昨天我的一个技术交流群里发了一段代码，涉及的是**变量和函数的声明提升**，执行结果非常让人迷惑，大家讨论许久，还是有地方解释不清楚，到最后，还是发布到`stack overflow`，通过大佬解惑才弄明白，这里我再整理一下。

## 我的思路
逐个解析一下讨论到的案例：

>没有特别说明的，运行环境就是是chrome77。

```javascript
  var a
  if(true) {
    a = 5
    function a() {}
    a = 0
    console.log(a)
  }

  console.log(a)
```
先整理一下我的思路：
- 首先，全局有一个`变量a`声明；
- 然后，有一个`if语句块`，是一个块级作用域；
- 块级作用域里面有`变量a`的赋值，还声明了一个`函数a`。

按照声明提升原则，`函数a`声明会被提升，这里没有使用`let`、`const`，所以我按`ES5`之前的声明提升规则去分析的。

没有块级作用域，提升到全局，而且函数声明优先于变量声明，所以会覆盖变量a的声明。

那么答案是：
```
0
0
```
很遗憾，**这个答案是错的**，至少现代浏览器是错的（我后来在IE的模拟环境运行，确实是这个结果）。

现在，公布一下**正确答案**：
```
0
5
```
这个答案非常让人迷惑不解：
- 第一个输出0，好理解
- 第二个输出5，到底是怎么回事？
    - 如果理解成：`let a = function(){}`，那么`a=5`是暂时性死区；
    - 如果理解成：`var a = function(){}`，那么会和上面一样，输出`0 0`；

## 大佬的解析

先看看代码运行解析[引用1]，我们可以把代码运行看成这样：
```javascript
var a¹;
 if (true) {
   function a²() {} // hoisted
   a² = 5;
   a¹ = a²; // at the location of the declaration, the variable leaves the block      
   a² = 0;
  console.log(a²)
}
console.log(a¹);
```

解释一下：
- 全局的`变量a`声明，这里没有问题。
- **重点1**，块级域的`函数a`声明提升，**提升到块级域顶部**。
- **重点2**，`a=5`，这里赋值的对象是本地的`函数a`，覆盖了。
- **重点中的重点**，执行到`函数a`声明处，本地的`变量a`覆盖了全局的`变量a`。按照[引用2]解释，这里也是提升，也就是说`函数a`**声明提升了两次**。到这里疑惑终于解开了！

## 总结
这里要说明两点：
- 块级作用域{}内的函数声明，在`ES5`中是非法的，在`ES6`中并没有严格规定（考虑到向下兼容性，参考阮老师的ES6入门），也就是这块依赖于实现[引用3]。
- 在项目里面，不要在块级作用域{}内声明函数，要在全局作用域或者函数作用域的顶层声明函数。

## 引用参考
1. [confused about function declaration in { }
](https://stackoverflow.com/questions/58619924/confused-about-function-declaration-in/58620404#58620404)
2. [What are the precise semantics of block-level functions in ES6?
](https://stackoverflow.com/questions/31419897/what-are-the-precise-semantics-of-block-level-functions-in-es6)
3. [web legacy compatibility semantics](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-block-level-function-declarations-web-legacy-compatibility-semantics)