---
title: 浅谈java中的i=i++
date: 2018-08-27 08:37:36
tags: java
catagories: article
cover: img/cover4.jpg
author: 
  nick: Vincent
  link: bloger.top

# 如果文章为转载文章，需要多加文章出处项
editor:
  name: Vincent
  link: https://bloger.top

# 首页每篇文章的子标题
subtitle: Java中的i=i++
---
#浅谈java中的 “i=i++;”

首先有一下代码：
```java
    public class Demo{
        public static void main(String []args){
            int i=12;
            i=i++;
            System.out.println(i);
        }
    }
```
你可以试想这段代码是多少  大多数同学可能会回答是13，然而事实并非如此，如下图结果：

![clipboard.png](https://segmentfault.com/img/bVbeHgO?w=985&h=561O)
在上图中，我首先进行了运行，可以看到结果，我们的i并不是如大多人猜测的那样为13输出 ，而是看上去没有做这个自增的操作，然而这是为什么呢？

于是，就有了后面反编译为字节码的操作，我们利用javap 指令反编译后可以看清楚其中发生的事情，有同学对其中的JVM指令不清楚，没关系，我这里先普及一下本文代码中所涉及的JVM指令；

**（这里的关键指令码在于main方法之后，因此只针对于下面的指令进行解释，其之前的是虚拟机自动构建的默认构造方法的指令码）**
  
   

 1.bipush:  将单字节的常量值(-128~127)推送至栈顶 
 2.istore:  将栈顶int型数值存入指定本地变量  
 3.iload:   将指定的int型本地变量推送至栈顶 
 4.iinc： 该指令用于对本地(局部)变量进行自增减操作。该指令第一参数为本地变量的编号，第二个参数为自增减的数量  
 5.getstatic  获取指定类的静态域，并将其值压入栈顶(到这个位置之后就是调用静态对象out打印的操作不再赘述，感兴趣的同学可以在本文末尾附上的链接的去了解一下)

知道基本的几个指令之后我们来分析上图中关键指令的意思吧：

 1. bipush 12这里是把12推至栈顶 栈里存储了12 这个int数值
 2. istore_1 这里是把12弹出给第二个（备注:第1个本地变量应该是this）本地变量 即i=12
 3. iload_1 这里是把第二个本地变量（i）的值推入栈顶 此时栈顶为12
 4. iinc 1,1 自然是i=i+1 即i++  这个时候i=13
 5. istore_1这里是把栈顶值弹出给本地变量i，这个时候 i又被赋值为 12

因此最终 打印出来的时候i为12。

[CSDN一老哥整理的JVM指令集合，很方便查询学习][1]

      


  [1]: https://blog.csdn.net/hudashi/article/details/7062781