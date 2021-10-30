---
title: mybatis
date: 2020-02-28 12:01:08
tags:
categories:
---

# Mybatis

## 结构 

```mermaid
graph LR
Configuration -->  SqlSession --> Executor --> Mapper  
```

 	mybatis 主要在于三块  configuration作为配置管理，sqlsession用于数据连接管理，以及执行分工。

