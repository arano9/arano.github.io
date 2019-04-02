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

1. View的唯一责任是按照Presenter的指示绘制UI。这是应用程序愚蠢的地方。
2. View将所有用户交互委派给其Presenter。
3. View永远不会直接与Model通信。
4. Presenter负责将View的要求委派给Model，并指示View对特定事件的操作。
5. Model负责从服务器，数据库和文件系统中获取数据。  

## 于是，MVP应该是这样的：

1. Activity，Fragment和CustomView充当应用程序的View部分。
2. 每个View都有一对一的Presenter关系。
3. View通过接口与其Presenter通信，反之亦然。
4. 模型分为几个部分：ApiHelper，PreferenceHelper，DatabaseHelper和FileHelper。这些都是DataManager的助手，它实际上绑定了所有Model部分。
5. Presenter通过接口与DataManager通信。
6. DataManager仅在被询问时使用。
7. 演示者无法访问任何Android的api。

## 细说架构
![MVP架构](https://github.com/Vincent9101/Vincent9101.github.io/blob/HEXO/source/images/MVP_architecture.png?raw=true
)  
- View:应用程序的一部分呈现UI并接收来自用户的交互。Activity，Fragment和CustomView构成了这一部分。
- MVPView（interface）:它是一个由View实现的接口。它包含暴露给其Presenter进行通信的方法。
- Presenter: 它是View的决策对象，是一个纯java类，无法访问Android API。它接收从其View传递的用户交互，然后根据业务逻辑做出决策，最后指示View执行特定操作。它还与DataManager通信，以获取执行业务逻辑所需的任何数据。
- MvpPresenter（interface）：它是一个接口，由Presenter实现。它包含暴露给其通信视图的方法。
- AppDbHelper：数据库管理和与数据库相关的所有数据处理都在应用程序的这一部分完成。
- DbHelper（interface）：它是由AppDbHelper实现的接口，包含暴露给应用程序组件的方法。该层解耦DbHelper的任何特定实现，因此使AppDbHelper成为即插即用单元。
- AppPreferenceHelper：它就像AppDbHelper，但是被赋予了从android共享首选项读取和写入数据的工作。
- PreferenceHelper（interface）：该接口就像DbHelper一样，但是由AppPreferenceHelper实现。
- AppApiHelper：它管理网络API调用和API数据处理。
- ApiHelper：它就像DbHelper一样，但是由AppApiHelper实现。
- DataManager：它是由AppDataManager实现的接口。它包含为所有数据处理操作公开的方法。理想情况下，它会委派所有Helper类提供的服务。对于此DataManager接口，扩展了DbHelper，PreferenceHelper和ApiHelper接口。
- AppDataManager：它是应用程序中任何数据相关操作的一个联系点。DbHelper，PreferenceHelper和ApiHelper仅适用于DataManager。它委派特定于任何Helper的所有操作。
## 架构之间的通信模式
- Application类通过传递DbHelper，PreferenceHelper和ApiHelper引用来实例化AppDbHelper（进入DbHelper变量），AppPreferenceHelper（进入PreferenceHelper变量），AppApiHelper（进入ApiHelper变量），最后运行AppDataManager（进入DataManager变量）。
- View组件通过MvpPresenter引用实例化其Presenter。
- Presenter接收它View组件并通过MvpView引用它。Presenter还接收DataManager。
- DataManager作为单例实例存在。