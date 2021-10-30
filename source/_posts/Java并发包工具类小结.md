---
title: Java并发包工具类小结
date: 2021-10-05 14:03:36
catagories: article
tags: Java
---



更新下，看源码遇到的一些问题：

unparkSuccessor从链表尾部后面遍历的原因:  https://www.zhihu.com/question/50724462/answer/123776334  
Doug Lea AQS framwork:  http://gee.cs.oswego.edu/dl/papers/aqs.pdf  



-------

废话不多说，AQS 无锁队列，队头cas抢锁，队尾cas排队就不多说了，下面就将源码三个组件的基本意思总结一下。

不难看出，其实都是利用AQS模板方法（链表队列结构），定义各自对资源的抢夺和释放



##### **CountdownLatch**

创建的时候给予state 一定量
await方法等待state到0，tryAcquire
countdown 每次都是对state的CAS自减，tryRelease


##### **Semaphore**

创建的时候给予state一定量
acquire每次都是对state 自减cas  
release每次都是对state  自增cas 

##### **CyclicBarrier**

利用的ReentrantLock 以及trip Condition
await每次都是 对内部parties加锁，然后利用 tripCondition进行count状态等待，
直到为0以后，breakBarrier，唤醒所有tripCondition 然后reset parties

