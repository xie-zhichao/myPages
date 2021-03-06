---
title: Node.js + TypeScript 开发服务端是什么样的体验
date: 2020-01-05 15:46:09
tags:
  - JavaScript
  - Node.js
  - Graphql
  - Koa2
categories: Node.js
---
> `Node.js + TypeScript` 开发服务端是什么样的体验，和传统的服务端语言 `Java` 有什么不同，我来说几点我的感受。

## Node.js特性
`Node.js` 和传统的后端语言 `Java` 在设计上是有很大区别的。**一个是动态语言，一个是静态语言；一个是单线程模型，一个是多线程模型**。

`Node.js` 其实不是一门语言，它是 `Javascript` 的服务器运行时环境, 在它出现之前 `Javascript` 只能运行在浏览器中。在这里我们也可以这么说：**`Node.js`就是可以写服务端的`Javascript`**。

`Node.js`是单线程模型，并且是事件驱动和异步I/O(底层是基于c语言实现的`libuv库`)，这个是它最基本的特点，和它相似的是Nginx。这个特点决定来它适合的场景：**I/O密集、少计算的应用**。

> 举个例子：`Node.js`的线程模型就像是麦当劳店里面的负责下单的服务员，客人排队下单，每个客人下完单就在旁边等餐，服务员则继续为下一个客人下单。同时，期间如果发现餐柜有客人的快餐准备好了，就端给旁边等候的客人。  
我们会发现：虽然只有一个人服务，但是整个过程非常高效。

我们来分析一下原因：麦当劳的服务员虽然服务的客人非常多，但是花费在每个客人身上的时间是非常短的，所以整体上非常高效。同时短板也很明显：如果碰问题顾客，下单非常慢，排队的客人就会拥堵。

其实还有一个重中之重：**麦当劳服务员能如此高效，要依靠后面提供支持的强大的配送和运营系统。**

`Java`则是多线程模型，与`Node.js`相比，它就是高级餐厅，一对一服务，互不影响，当然成本也是高的。

以上这些都是基本运行模型的特性，实际使用还是要看场景给设计。

## TypeScript黑魔法
`Javascript` 是动态语言，用于脚本等场景时，非常灵活高效。但是业务逻辑场景时，规范性和严谨性就不足了，主要表现在：变量、参数、返回值的类型不确定，容易被用错；模块之间的调用、对外提供接口时，契约定义不明确。

有了`TypeScript`，`ES6`之后，上述问题就有了明显改善，同时`TypeScript`强大的类型系统，让设计和编写仍然拥有非常好的灵活性和设计空间。

`Java`有的接口、泛型、类、继承和多态，`TypeScript`一应俱全，同时`TypeScript`还有交叉类型、联合类型、映射类型、类型推导和条件类型等黑魔法。

另外，因为`TypeScript` 是 `Javascript`的超集，所以它也具备函数式编程的能力。函数式编程的抽象能力非常强大，能提供很多有用的特性：比如`副作用消除`，`引用透明`，`高阶函数`，`闭包`，`模式匹配`，`延迟求值`等等。


## 生态
`Node.js + TypeScript`被越来越多的公司用于核心业务，比如：国外的Paypal、Linkedin、Walmart、Netflix等，国内的阿里、腾讯、网易等。
`npm`托管的包数量已经超越其他包托管器。

框架方面，国外的`Nest.js`，国内的`Midway`已经有了很多成熟应用案例。`Nest.js`从编程体验上，也和`Spring`非常相似。

## 更多
`Node.js`后续会日趋成熟，应用范围也会越来越广泛。当然，短期内还不会替`Java`的企业服务主力位置，毕竟能力特性不一样，并且生态成熟度、基础设施成熟度还是差了不止一个量级的。

`Node.js`最新版支持了`Worker Thread`，`诊断报告`，`Heap Dump`，这几个特性让它在生产环境的使用越来越得心应手，同时还有大幅的速度提升，后续表现可期。

