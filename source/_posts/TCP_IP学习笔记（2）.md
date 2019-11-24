---
title: TCP/IP学习笔记（2）
date: 2018-09-27 22:11:28
tags: TCP/IP
catagories: note
cover: https://images.unsplash.com/photo-1537896113971-b579bf11ea7c?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=dc7aada1dab0cca42020c9aee0cef5ee&auto=format&fit=crop&w=1000&q=60
author: 
  nick: Vincent
  link: bloger.top

# 如果文章为转载文章，需要多加文章出处项
editor:
  name: Vincent
  link: https://bloger.top

# 首页每篇文章的子标题
subtitle: TCP/IP学习2
---


#  第二章 链路层
## 链路层目的
1. 为IP模块发送和接收IP数据报；
2. 为ARP模块发送ARP请求和AR应答;
3. 为RARP发送RARP请求和接收RARP应答；  

## 以太网和IEEE 802封装
- 以太网是当今TCP/IP采用的主要的局域网技术。采用CSMA/CD的媒体介入方法（带有冲突检测的载波侦听多路接入），速率10Mb/s,地址48bit。
- 下面是数据帧格式     

![IEEE 802.2/802.3（RFC 1042）和以太网的封装格式（RFC 894）](http://docs.52im.net/extend/docs/book/tcpip/vol1/2/images2/52im_1.png)  

IEEE 802.2/802.3（RFC 1042）和以太网的封装格式（RFC 894）

## 尾部封装
这是另外一种以太网的封装格式，称作**尾部封装**，通过调整IP数据报中字段的次序提高性能，在以太网数据帧中，开始的那部分是变长的字段（IP首部和TCP首部）。把它们移到尾部（在CRC之前），这样当把数据复制到内核时，就可以把数据帧中的数据部分映射到一个硬件页面，节省内存到内存的复制过程。TCP数据报的长度是512字节的整数倍，正好可以用内核中的页表来处理。两台主机通过协商使用ARP扩展协议对数据帧进行尾部封装。这些数据帧需定义不同的以太网帧类型值。  
**注意：现在的尾部封装遭到反对，已经过时。**  
## SLIP：串行线路IP
SLIP（Serial Line IP）是一种在串行线路上对IP数据报进行封装的简单形式，它适用于家庭计算机几乎都有的RS-232串行端口和高速调制调制器接入Internet。
- SLIP的帧格式：
    - IP数据报以称作END（0xc0）的字符作为结束，为了防止线噪声被当做数据，开始的时候也会使用END字符。
    - 如果IP报文中的某个字符为END，使用0xdb和0xdc来取代字符，0xdb这个字符是SLIP的ESC字符，与ASCII的ESC有区别。
    - 如果IP报文中拥有SLIP中ESC（0xdb），那么就需要连续传输0xdb和0xdd来取代。  
![SLIP数据报封装](http://docs.52im.net/extend/docs/book/tcpip/vol1/2/images2/52im_2.png)  

- SLIP是一种简单的帧封装方法所以拥有如下**缺点**：
    1. 发送双方必须知道对方的IP地址，不然无法发送。
    2. 数据帧中没有类型字段。如果一条串行线路用于SLIP那么不可以同时使用其他协议。
    3. SLIP没有在数据帧中检验和。若是传输报文被线路噪声影响，只能通过上层协议来发现纠正。

## 压缩的SLIP
由于串行线路的速率较低，通信常常是交互式的，因此在SLIP线路上有许多的TCP分组进行交换，常为了传送一个字节的数据而添加20个字节的IP首部和20个字节的TCP首部。  
因此，特地提出了一个压缩SLIP（CSLIP）的标准,可以把40个字节压缩到3到5个字节，其CSLIP的每一端可以为维持16个TCP连接。 

## PPP（点对点协议）
PPP修改了SLIP协议中所有缺陷，PPP分为三个部分：
- 在串行链路上封装的IP数据报的方法。既支持数据为8位和误奇偶检验的异步模式，还支持面向比特的同步链接。   
- 建立、配置以及测试数据链路的链路控制协议（LCP：Link Control Protocol）。允许通信双方进行协商，以确定不同的选项。
- 针对不同网络层协议的网络控制协议（NCP：networlk control protocol） 。例如：IP NCP允许通信双方商定是否对报文首部进行压缩。    - PPP数据各格式：
![](http://docs.52im.net/extend/docs/book/tcpip/vol1/2/images2/52im_3.png)  
- **PPP对比SLIP**
    - (1)PPP支持在单根串行线路运行多种协议，不只IP协议；
    - （2）每一帧都有循环冗余检验；
    - （3）通信双方可以进行IP地址的动态协商（使用IP网络控制协议）；
    - （4）付出的大家是在每一帧的首部增加3个字节，建立链路时候要多发送几帧协商数据，以及更复杂的实现。

## 环回接口（Loopback Interface）
环回接口用以在同一台主机上的客户程序和服务器程序通过TCP/IP进行通信，A类网络号127就是为环回接口预留的。以下是环回接口处理IP数据报的简单过程：  
![](http://docs.52im.net/extend/docs/book/tcpip/vol1/2/images2/52im_4.png)    
**注意：**
1. 传给环回地址（127.0.0.1）的数据均作为IP输入。
2. 传给广播地址或者多播地址的数据报复制到一份到环回接口，然后送到以太网上，这是由于广播传送和多播传送的定义包含主机本身。
3. 任何传给该主机IP地址的数据均送到环回接口。

## 最大传输单元MTU
由于以太网对数据帧的长度有一个最大限制。链路层的这个特性称作MTU，**最大传输单元**。  
因此，如果数据长度大于链路层的MTU，那么IP层就需要把数据报进行分片。一下是一些限制标准：  
![](http://docs.52im.net/extend/docs/book/tcpip/vol1/2/images2/52im_5.png)  

## 路径MTU
- 同一个网络上的不同主机进行通信，该**网络**的MTU是很重要的。如果**不同**网络之间的主机通信，重要的就是主机路径中的最小MTU，即路径MTU。
- 主机之间的MTU不一定是一个**常数**，取决于当时选择的路由，而选路不一定是对称的，因此路径MTU两个方向也不一定一样。

## 串行线路吞吐量计算 
若线路速率是9600b/s，一个字节是8bit,加上1bit开始与1bit结束，其线路速率为960B/s。