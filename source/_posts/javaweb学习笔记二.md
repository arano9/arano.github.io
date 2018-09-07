---
title: javaweb学习笔记二
date: 2018-08-27 08:40:33
tags: java
catagories: article
cover: cover.jpg
author: 
  nick: Vincent
  link: bloger.top

# 如果文章为转载文章，需要多加文章出处项
editor:
  name: Vincent
  link: https://bloger.top

# 首页每篇文章的子标题
subtitle: javaweb笔记2
---
## web应用应用中URL中的路径问题 ##

 - ①在设置页面路径的时候设置一定要用contextPath（可以通过application.getContextPath（）获得）进行拼接，不然会有可能出现路径错误的问题，这是由于在不同的位置"/"符号会有不同的意思。

 - ②JavaWeb中“/”符号有时候会有时会是站点的根目录，有时候表示当前web应用的根目录，
 >**当该路径交由servlet容器处理的时候就是当前web应用的根目录，当该路径交由浏览器处理的时候就是该站点的根目录**
 >***http://localhost:8080/***MVC/first.jsp :其中斜体加粗部分就是站点根目录。

## 重复提交问题 ##

（1）使用重定向解决重复提交问题


 - 当表单提交到一个servlet，然后servlet又用***请求转发***的方式到了另外一个页面，此时浏览器上的路径还是servlet 的路径，如果点击了刷新的时候就会进行重复提交。
  >重复提交是会使服务器的负担变大，因此当需要用servlet相应页面的时候如果没有使用的其request对象的必要时，应当使用重定向。

（2）使用session与request结合解决重复提交问题


 - 在原表单生成一个随机值作为标记，然后把该值放在session的属性中，并放在隐藏域中提交到后台，在后台中获取session该属性比较隐藏域的值，若一样则说明不是重复提交，然后在session中把该值删除。