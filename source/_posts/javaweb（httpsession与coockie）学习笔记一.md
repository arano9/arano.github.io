---
title: javaweb（httpsession与coockie）学习笔记一
date: 2018-08-27 08:43:27
tags: java
catagories: article
cover: img/cover1.jpg
author: 
  nick: Vincent
  link: bloger.top

# 如果文章为转载文章，需要多加文章出处项
editor:
  name: Vincent
  link: https://bloger.top

# 首页每篇文章的子标题
subtitle: javaweb笔记1
---
# **cookie的作用域**

cookie只能够传到当前目录以及以下的目录，不可以传到上一目录。

# **HttpSession的创建**

1. HttpSession的生命周期:

1).什么时候创建HttpSession对象  
①.对于JSP:是否浏览器访问服务端的任何一个JSP,服务器都会立即创建一个HttpSession对象呢?不一定。
>若当前的JSP是客户端访问的当前WEB应用的第一个资源,且JSP的page指定的session属性值为false,
则服务器就不会为JSP创建一个HttpSession对象;

>若当前JSP不是客户端访问的当前WEB应用的第一个资源,且其他页面已经创建一个HttpSession对象,
则服务器也不会为当前JSP页面创建一个HttpSession对象,而回会把和当前会话关联的那个HttpSession对象返回给当前的JSP页面.

②.对于Serlvet:若Serlvet是客户端访问的第一个WEB应用的资源,
>
则只有调用了request.getSession()或request.getSession(true)才会创建HttpSession对象
当request.getSession(false)的时候如果没有与当前页面关联的HttpSession对象的时候就会返回null，
当request.getSession（true）（request.getSession（boolean create））的时候就会直接返回一个HttpSession对象 同request.getSession（）。

<b>总结:当客户端访问web第一个资源并且当前JSP或者Servlet没有禁用当前页面的默认创建的Session对象或者没有调用request.getSession(false)方法的时候就会返回一个HttpSession对象。</b>

2). session="false" 到底表示什么意思?当前JSP页面禁用session隐含变量!但可以使用其他的显式的HttpSession对象

# **HttpSession的销毁**

 1. 调用session对象的invalidate（）方法会使session马上失效。
 
 2. 当过了session的有效时间。
    可以通过设置其有效时间来加速失效：调用session.setMaxInactiveInterval（int             
    seconds），
    或者配置web.xml文件的配置：
    <session-config><session-timeout>**minutes**</session-timeout> </session-config>


# **URL重写**

**浏览器把cookie被禁用的时候一般用URL重写的方法进行sessionID的跟踪，以便后续的上下文（即统一web中）可以使用同一sessionID访问同一session对象，其中进行URL重写的方法有两个：**
 - encodeURL（String url）***resonpse.encodeURL(String url)***
 - encodeRedirectURL（String url）***response.sendRedirect(response.encodeRedirect(String url))***
  >**相同点：**：两个会在cookie禁用的情况下对URL进行加上JsessionId的重写，当然，如果没有禁用，二者也不会对URL进行重写。
  **不通点：**  后者会对URL指向的资源进行判断，如果不是本web应用里的资源路径也不会进行SessionID的添加，因此该函数URL参数必须是绝对路径。
 