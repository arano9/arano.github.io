---
title: java虚拟机运行数据区域
date: 2018-10-24 23:11:28
tags: java
catagories: note
cover: https://images.unsplash.com/photo-1537988489137-33d67a16f3ec?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=78fee676b893671722807e77848176f5&auto=format&fit=crop&w=500&q=60
author: 
  nick: Vincent
  link: bloger.top

# 如果文章为转载文章，需要多加文章出处项
editor:
  name: Vincent
  link: https://bloger.top

# 首页每篇文章的子标题
subtitle: java虚拟机学习笔记（1）
---
## 运行数据区
### 程序计数器 （Program Counter Register） 
是一块较小的内存空间，可以看做是当前线程所执行的字节码的行号指示器。是线程私有的，如果正在执行的是native方法这个计数器值为空Undefined。这是java虚拟机规范中唯一没有规定任何OutOfMemory情况的区域。
### java虚拟机栈 （Java Virtaul Machine Stack）
线程私有的，生命周期与线程相同，描述java方法执行的内存模式，每个方法在执行的同时，创建一个栈帧(stack frame)用于存储局部变量表，操作数栈，动态链接，方法出口等信息。  

在java虚拟机规范中，对这个区域有两种异常状况：
- 如果线程请求的栈的深度大于虚拟机所允许的深度，就会抛出stackoverflowErroe的异常
- 如果虚拟机可以动态扩展深度，如果扩展时候无法申请到足够的内存就会抛出OutOfMemoryEoor的异常。

### 本地方法栈 （Native Method Stack） 
作用类似虚拟机栈，区别在于虚拟机栈为虚拟机执行java方法，本地方法栈则为虚拟机使用到的Native方法服务。

### java堆 （Java Heap） 
java Heap是java虚拟机所管理的内存中最大的一块。是被所有的线程共享的一块内存区域，用于存放对象实例。    

从内存分配的角度看java堆中可能划分出多个线程私有的分配缓冲区（Thread Local Allocation Buffer TLAB)。不论如何划分 里边存放的始终是对象实例，划分是为更好的管理回收内存，或者分配内存。

java堆是垃圾收集器的主要管理区域，因此有时候也被称为GC堆，当堆中没有内存完成实例分配的时候，并且堆无法进行扩展的时候就会抛出OutOfMemoryError异常。  

### 方法区（Method Area）
方法区（Method Area）与Java堆一样，是线程共享的内存区域，用于存储已经被虚拟机加载的类信息，常量，静态变量，即时编译器编译后的代码等数据，

#### 运行时常量池（Runtime Constant Pool）  
是***方法区的一部分***，Class文件中除了类的版本、字段、方法、接口还有一项就是常量池，用于存放编译期生成的各种字面量和符号引用，这部分内容将在类加载后进入方法去的运行时常量池存放。
### 直接内存(Direct Memory)  
直接内存并不是虚拟机运行是数据区的一部分，也不是java虚拟机规范中定义的内存区域。但是由于使用频繁使用，也可能导致OutOfMemoryError。

JDK1.4中加入了NIO类，引入了基于通道与缓冲区的I/O方式，它可以使用Native函数库直接分配堆外内存，然后通过一个存储在java堆中DirectByteBuffer对象作为这块内存的引用进行操作，提高了性能，避免了java堆和Native堆中来回复制数据。