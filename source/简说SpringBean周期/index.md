---
layout: page
title: 简说SpringBean周期
date: 2021-04-26 19:59:10
tags: java spring
---

### 你真的了解IOC么？

### 依赖注入到底又是什么呢？

##  简说SpringBean

1. BeanFactory初始化 
2. BeanFactory后置处理器执行
3. Bean初始化（反射）
4. Populate Fileld
5. NameAware BeanFactoryAware AppAware 
6. BeanPostProcessor#beforeInitialization
7. InitializingBean#afterPropertiesSet
8. initMethod@PostConstruct
9. BeanPostProcessor#afterIInitialization
10. DisposableBean#destroy
11. destroyMethod@PreDestroy

```java
// FileName: HelloWorld.java
public class HelloWorld {
  // Java 入口程序，程序从此入口
  public static void main(String[] args) {
    System.out.println("Hello,World!"); // 向控制台打印一条语句
  }
}
```

单例：到spring容器，原型即创即用