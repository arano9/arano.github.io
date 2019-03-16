---
title: Android_MVP架构
date: 2019-03-16 00:41:02
tags: android
categories: note
---
# 什么是MVP(Model-View-Presenter)设计模式
MVP设计模式是一组指导原则，如果遵循这些指导原则，则将代码解耦为可重用性和可测试性。它根据应用程序组件的角色划分应用程序组件，称为关注点分离。MVP将应用程序划分为三个基本组件：

- 模型：它负责处理应用程序的数据部分。
- 视图：它负责在屏幕上显示具有特定数据的视图。
- 演示者：它是连接模型和视图的桥梁。它还充当View的讲师。

## MVP为上述组件提供了一些**基本规则**，如下所示：

1. View的唯一责任是按照Presenter的指示绘制UI。这是应用程序的一个愚蠢部分。
2. View将所有用户交互委派给其Presenter。
3. View永远不会直接与Model通信。
4. Presenter负责将View的要求委派给Model，并指示View对特定事件的操作。
5. Model负责从服务器，数据库和文件系统中获取数据。  
## 于是，MVP一个个是这样的：
1. Activity，Fragment和CustomView充当应用程序的View部分。
2. 每个View都有一对一的Presenter关系。
3. View通过接口与其Presenter通信，反之亦然。
4. 模型分为几个部分：ApiHelper，PreferenceHelper，DatabaseHelper和FileHelper。这些都是DataManager的助手，它实际上绑定了所有Model部分。
5. Presenter通过接口与DataManager通信。
6. DataManager仅在被询问时使用。
7. 演示者无法访问任何Android的api。

## 细说架构
![MVP架构](https://raw.githubusercontent.com/Vincent9101/Vincent9101.github.io/HEXO/source/images/MVP_architecture.png
)