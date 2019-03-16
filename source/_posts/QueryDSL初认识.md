---
title: QueryDSL查询框架
date: 2019-01-21 18:45:51
tags: java
categories: article
---
# QueryDSL查询框架初认识
&ensp;&ensp;QueryDSL是一个通用的查询框架，通过JavaAPI方式构建安全的SQL查询，也可以用到NoSql数据库中，QueryDSL查询框架可以支持ORM框架或者SQL平台上以一种通用的API方式构建SQL。目前QueryDSL支持的平台有JPA，JDO，SQL,JavaCollections,RDF，MongoDB等。  
## QueryDSL优势  
   1. Querydsl支持代码自动完成，因为才纯Java API编写查询，因此主流Java IDE对起的代码自动完成功能支持几乎可以发挥到极致（因为是纯Java代码，所以支持很好） 
   2. Querydsl几乎可以避免所有的SQL语法错误（当然用错了Querydsl API除外，因为不写SQL了，因此想用错也难） 
   3. Querydsl采用Domain类型的对象和属性来构建查询，因此查询绝对是类型安全的，不会因为条件类型而出现问题 
   4. Querydsl采用纯Java API的作为SQL构建的实现可以让代码重构发挥到另一个高度
   5. Querydsl的领一个优势就是可以更轻松的进行增量查询的定义 
