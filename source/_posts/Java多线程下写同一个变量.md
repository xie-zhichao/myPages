---
title: Java多线程下写同一个变量
date: 2018-01-25 16:33:53
tags:
  - Java
  - 多线程
categories: Java
---
## 场景
为了提高程序并发效率，多线程是必不可少的方案。
不过，多线程虽好处很多，但是，复杂度也会上一个数量级。本篇要分析的场景就是：多线程修改同一个对象的本地属性。
## 问题点
> java多线程存在线程本地内存、主存同步的问题：线程会先加载主存变量到本地内存，然后再做处理。如果不做同步处理，不同的线程修改的可能是线程本地内存，这会导致最终线程本地内存、主存同步时互相覆盖。

## 案例评析
### 不作任何同步措施
``` java
public class Exam implements Runnable {
    Integer n = 0;
	
    @Override
    public void run() {
        for (int i=0; i<10000; i++) {
			n++;
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Exam exam = new Exam();
        Thread a = new Thread(exam);
        Thread b = new Thread(exam);
        a.start();
        b.start();
        a.join();
        b.join();
        System.out.println(exam.n);
    }
}
```
> 这种写法，完全没考虑到多线程的同步问题，结果肯定会出问题的

### 使用volatile
``` java
public class Exam implements Runnable {
    volatile Integer n = 0;
	
    @Override
    public void run() {
        for (int i=0; i<10000; i++) {
			n++;
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Exam exam = new Exam();
        Thread a = new Thread(exam);
        Thread b = new Thread(exam);
        a.start();
        b.start();
        a.join();
        b.join();
        System.out.println(exam.n);
    }
}
```
> volatile修饰的变量，修改时会同时更新主存，看似没问题。这里我们要get到一个点：n++并不是原子操作，这个语句是分两步执行的，先读取，再修改。这其中有两个原子操作。

### 使用synchronized
``` java
public class Exam implements Runnable {
    Integer n = 0;
	
    @Override
    public void run() {
        for (int i=0; i<10000; i++) {
            synchronized (this) {
                n++;
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Exam exam = new Exam();
        Thread a = new Thread(exam);
        Thread b = new Thread(exam);
        a.start();
        b.start();
        a.join();
        b.join();
        System.out.println(exam.n);
    }
}
```
> 这个方案是可行的，synchronized代码块同时只有一个线程进来。当线程进来，读取变量、更新变量时，其他线程已经更新完变量。
*这里，synchronized (this)不能换成synchronized (n)，原因大家可以去探究一下*
