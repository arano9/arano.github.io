---
title: TCP/IP学习笔记（6）
date: 2018-09-28 23:11:28
tags: TCP/IP
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
subtitle: TCP/IP学习6
---
# Ping程序 
## 概述
ping程序是为了测试另一台主机是否可达。该程序发送一份ICMP回显报文交给主机，并且等待返回ICMP回显应答。Ping还提供了检测IP记录路由和时间戳选项的机会。
## Ping程序
一般称发送回显请求的ping程序为客户，而被ping的主机为服务器。ICMP回显请求和回显应答报文如下 ：  
![ICMP回显请求和应答报文格式](http://docs.52im.net/extend/docs/book/tcpip/vol1/7/images2/52im_net_1.png)      

# Traceroute程序
## Traceroute的作用与实现原理
Traceroute程序可以让我们看到IP数据报从一台主机传到另一台主机经过的路由。可以看到IP源路由选项。  

Traceroute程序使用ICMP报文和IP首部中的TTL字段（生存周期）。TTL字段是由发送端初始设置的一个8bit字段。一般发送ICMP回显应答的时候把TTL设置为最大值225。

每个处理数据报的路由器都要把TTL的值减去1或者减去数据报在路由器中停留的秒数。

TTL字段是为了防止数据报在选路时无休止地在网络中流动。当路由器收到一份IP数据报的时候，若果TTL字段是0或者1，则路由器不转发该数据报，路由器会将数据报丢弃，并且给信源机发送一份ICMP“超时信息”,Traceroute程序的关键在于包含这份ICMP信息的IP报文的信源地址是路由器的IP地址。

### 实现
traceroute程序发送一份UDP数据报给目的主机，但是选择了一个不可能的值作为UDP端口号（>3000），使目的主机的任何一个应用程序都不可能使用这个端口，因为当数据报到达时，目的主机的UDP模块将产生一份“端口不可达”错误的ICMP报文，这样Traceroute程序索要做的就是区分ICMP报文是超时还是不可达，以判断什么时候结束。

