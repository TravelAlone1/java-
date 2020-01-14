[TOC]

# 深入详解Synchronized同步锁的底层实现

> Synchronized

synchronized 翻译为中文的意思是**同步，**也称之为”同步锁“。

synchronized的作用是保证在同一时刻，**被修饰的代码块或方法只会有一个线程执行**，以达到保证并发安全的效果。

> Synchronized的使用

**1.synchronized的3种使用方式**

-  **修饰实例方法**：作用于当前实例加锁
-  **修饰静态方法**：作用于当前类对象加锁
-  **修饰代码块：**指定加锁对象，对给定对象加锁

2.**synchronized的代码范例**

![](https://note.youdao.com/yws/public/resource/4de8bbd13c709899bb2e676cb12a84ef/xmlnote/2BBDD02F650C4A1EBCF9E3842F974BBE/9671)

> ## Synchronized的底层实现

synchronized的底层实现是完全依赖与JVM虚拟机的。

所以谈synchronized的底层实现，就不得不谈数据在JVM内存的存储：**Java对象头，以及Monitor对象监视器。**

**1.Java对象头**

在JVM虚拟机中，对象在内存中的存储布局，可以分为三个区域:

-  对象头(Header)
-  实例数据(Instance Data)
-  对齐填充(Padding)