---
title: Java不可变对象(immutable object)
date: 2018-04-05 18:05:27
tags:
  - Java
  - 多线程
  - 不可变对象
categories: Java
---
# 题记
在前面一篇[《Java多线程下写同一个变量》](/2018/01/25/Java多线程下写同一个变量/) 最后有一个问题，用变量n(String类型)做锁对象，为什么不行呢？这里涉及到一个概念：不可变对象。

# 不可变对象
引用Java Doc：
> An object is considered immutable if its state cannot change after it is constructed.
一个对象，在创建之后，状态不能修改，认为它是不可变对象。

常见的不可变对象是：String，Integer的对象。
看一段代码：
```java
class MyTest{
    public static void main(String[] args) {
        String str = "head";
        System.out.println(strTest(str));
        System.out.println(str);
    }

    public static String strTest(String str) {
        str += "-tail";
        return str;
    }
}
```

> 输出结果：
head-tail
head

这个表现得和传值一样。

## 五大原则
不可变更对象需遵循如下原则：
* immutable对象的状态在创建之后就不能发生改变，任何对它的改变都应该产生一个新的对象。
* Immutable类的所有的属性都应该是final的。
* 对象必须被正确的创建，比如：对象引用在对象创建过程中不能泄露(leak)。
见如下举例
```java
public final class ImmutableDemo {  
    private final int[] myArray;  
    public ImmutableDemo(int[] array) {  
        this.myArray = array; // wrong, you can change myArray by array 
    }  
}
```
* 对象应该是final的，以此来限制子类继承父类，以避免子类改变了父类的immutable特性。
* 如果类中包含mutable类对象，那么返回给客户端的时候，返回该对象的一个拷贝，而不是该对象本身（该条可以归为第一条中的一个特例）

## 优缺点
* 优点，天生线程安全，因为无论在哪里修改它，都会产生新的对象
> 这里说明一下，并不是说String就可以随便用于多线程共享，线程安全的只是当时那个String对象，你的String变量是个引用值，**当你改动时，这个引用值还是在变的，每改一次，都会指向一个新对象**。
* 优点，用于HashMap的key，hashcode是稳定的。**这里还有一个概念：字符串常量池，字符串在jvm运行环境内部有一个池子，String对象都是指向这个池子，相同的字符串指向相同的地址**。
* 缺点，以String作例子，如果你频繁改动，会产生过多新对象，影响性能。可以考虑用StringBuilder(线程不安全)和StringBuffer(线程安全)

# 上一篇文章的答案
看完上面的解释，应该很好理解了，n每改一次，都会指向新的对象，对象锁就无效了。