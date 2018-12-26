---
title: TCP/IP学习笔记（5）
date: 2018-09-28 23:11:28
tags: TCP/IP
catagories: note
cover: https://images.unsplash.com/photo-1538053975157-57791eb40d9b?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=fc37b176a04f8cd46149c7ce66d5baf8&auto=format&fit=crop&w=500&q=60
author: 
  nick: Vincent
  link: bloger.top

# 如果文章为转载文章，需要多加文章出处项
editor:
  name: Vincent
  link: https://bloger.top

# 首页每篇文章的子标题
subtitle: TCP/IP学习5
---

# 第六章 ICMP：Internet控制报文协议
## 概述
ICMP经常被认为是IP层的组成部分，他传递差错报文以及其他需要注意的信息， ICMP报文通常被IP层或者更高层次协议（TCP/UDP）使用，甚至返回给用户进程。

ICMP报文是在IP数据报内部传输的
![](http://docs.52im.net/extend/docs/book/tcpip/vol1/6/images2/52im_net_1.png)
  
 以下是ICMP报文格式  
![](http://docs.52im.net/extend/docs/book/tcpip/vol1/6/images2/52im_net_2.png)  

ICMP报文前4个字节都是一样的，但是剩下的其他字节不同，类型字段可以有15个不同的值，描述特定类型的ICMP报文，检验和字段覆盖整个报文。

## ICMP的报文类型
![ICMP报文类型](http://docs.52im.net/extend/docs/book/tcpip/vol1/6/images2/52im_net_3.png)  
以下情况不会导致ICMP差错报文：
1. ICMP差错报文（ICMP查询报文还是可能造成该ICMP差错报文的）
2. 目的地址是广播地址或者多播地址的IP数据报。
3. 作为链路层广播的数据报。
4. 不是IP分片的第一片。
5. 源地址不是单个主机的数据报，源地址不可以是零地址、环路地址、广播地址或多播地址。  
这些规则都是防止过去允许ICMP差错报文对广播分组响应所带来的广播风暴。

## ICMP地址掩码请求与应答
ICMP地址掩码请求用于无盘系统在引导过程中获取自己的子网编码，系统广播它的ICMP请求报文，无盘系统获取子网掩码的另一个方法是BOOTP协议。  
![ICMP地址掩码请求与应答报文](http://docs.52im.net/extend/docs/book/tcpip/vol1/6/images2/52im_net_4.png)

## ICMP时间戳请求和应答
ICMP时间戳请求允许系统向另一个系统查询当前的时间。