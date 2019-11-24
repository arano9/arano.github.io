---
title: springnboot初认识
date: 2019-03-13 09:52:48
tags: [SpringBoot,java]
categories: note
---
**1. 引入web模块**  
- pom.xml中添加支持web的模块：pom中默认拥有两个模块：spring-boot-starter（核心模块，包括自动配置支持，日志，YAML），spring-boot-starter-test（测试模块，包括 JUnit、Hamcrest、Mockito）。
```xml 
 <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
