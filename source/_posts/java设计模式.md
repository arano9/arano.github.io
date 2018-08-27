---
title: java设计模式
date: 2018-08-23 14:45:47
tags: java
catagories: article
cover: http://oxnuwmm3w.bkt.clouddn.com/hello-world.jpeg
author: 
  nick: Vincent
  link: bloger.top

# 如果文章为转载文章，需要多加文章出处项
editor:
  name: Vincent
  link: https://bloger.top

# 首页每篇文章的子标题
subtitle: java设计模式
---
 #  设计模式的的六大原则:
   学习设计模式之前最好先了解一下设计模式的设计原则：

 **1. 开闭原则（open close principle）**

 ***开放即指对扩展开放，对修改关闭*** 简而言之，就是扩展功能的时候应该尽量的不修改原有的代码。

 **2. 里氏代换原则（liskov substitution principle）**

  可以简单理解为派生类与基类的替换关系，一旦程序中出现基类，那么这个基类若是呗派生类替换了，也应该是合适的，并且对程序功能不受影响，该原则实际上是开闭原则的补充。 基类能真正复用，派生类也能够在基类的基础上增加新的行为。实现开闭原则的关键步骤就是抽象化，而基类与子类的继承关系就是抽象化的具体实现，所以里氏代换原则是对实现抽象化的具体步骤的规范。

**3. 依赖倒转原则（dependence inverse principle）**
****
这个原则是开闭原则的基础，具体内容：针对接口编程，依赖于抽象而不依赖于具体。

**4. 接口隔离原则（Interface Segregation Principle）**

使用多个隔离的接口，比使用单个接口要好，该模式出发点在与大一点的软件设计架构，便于维护升级，降低耦合度。

**5.迪米特法则，又称最少知道原则（Demeter Principle）**

一个实体应当尽量少地与其他实体之间发生相互作用，使得系统功能模块相对独立。

**6. 合成复用原则（Composite Reuse Principle）**
尽量使用合成/聚合的方式，而不是使用继承。



# 设计模式可以分为三大类：

 - ## 创建型模式（creational pattern）
   隐藏创建对象的创建逻辑，提供创建对象的接口，而非使用new关键字进行创建。

 - ### 工厂模式（factory pattern）

    隐藏创建的对象的逻辑，通过共同对的接口创建对象。
    
    
    实现demo结构如下图,实现demo代码点这里[这是我学习的时候写的demo][1]   
	![factory_pattern](https://segmentfault.com/img/bVbfibS?w=560&h=325)

 - ### 抽象工厂模式（abstract factory pattern）

   这个模式是工厂的共工厂 叫超级工厂模式还比较贴切，在抽象工厂模式中，接口是负责创建一个相关对象的工厂，不需要显式指定它们的类。每个生成的工厂都能按照工厂模式提供对象。  
    ![abstract_factory_pattern](https://segmentfault.com/img/bVbfj40?w=560&h=437)

 - ### 单例模式（singleton pattern）    
 一个类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象
1. 不支持线程的懒汉单例模式
```java
public class Singleton_unsupport_multithread {

	private Singleton_unsupport_multithread() {
		// TODO Auto-generated constructor stub
	**}

	private static Singleton_unsupport_multithread instance;

	public static Singleton_unsupport_multithread getSingleton() {

		if (instance == null) {
			instance = new Singleton_unsupport_multithread();
		}

		return instance;
	}

	public void println() {
		System.out.println("In unsupport_multithread");
	}

}

```
2. 支持多线程的懒汉单例模式
```java
public class Singleton_support_multithread {

	private Singleton_support_multithread() {
		// TODO Auto-generated constructor stub
	}

	private static volatile Singleton_support_multithread instance;

	/**
	 * 第一个判断可以减少锁住对象的情况 如果不为空直接返回 效率更高 
	 * 第二个判断就是懒加载的判断
	 * 
	 * @return
	 */
	public static Singleton_support_multithread getInstance() {
		if (instance == null) {
			synchronized (Singleton_support_multithread.class) {
				if (instance == null)
					instance = new Singleton_support_multithread();
			}
		}

		return instance;
	}

}

```
3. 饿汉单例模式
```java
/**
 * 
 * @author Vincent
 * 线程安全 但是容易产生垃圾对象，没有加锁执行效率会比较高 
 * 但不是懒加载 类一加载的时候就进行初始化 浪费内存
 *
 */
public class Singleton_hungry_man {

	private Singleton_hungry_man() {
		// TODO Auto-generated constructor stub
	}

	private static Singleton_hungry_man instance = new Singleton_hungry_man();

	public static Singleton_hungry_man getInstance() {

		return instance;

	}

}

```
4. 登记注册单例模式
```java
/**
 * 
 * @author Vincent
 * 登记注册式 单例
 * 这里其实与前面的饿汉单例模式有点类似 但是由于利用了ClassLoaderDe特性 
 * 使在不调用的getInstance的时候不会初始化instance 实现了懒加载
 *
 */

public class Singleton_register {

	private static class SingletonHolder {
		private static Singleton_register instance = new Singleton_register();
	}

	private Singleton_register() {
		// TODO Auto-generated constructor stub
	}

	public static final Singleton_register getInstance() {
		return SingletonHolder.instance;
	}

}

```

5. 枚举单例模式
```java
/**
 * 
 * @author Vincent
 * 讲道理哦 这个方式最方便的单例模式 
 * 利用枚举 自带支持 序列化机制  防止反序列化多次创建对象 可以适应多线程 然而却不怎么受待见 大多数人不用这个方式 可能和习惯有关系吧 = =   
 *
 */
public enum Singleton_enum {
		
	Instance;
	
}

```

 

 - ### 建造者模式（builder pattern）

    使用多个简单的对象一步一步构建成一个复杂的对象。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。值得一说的是感觉是这个设计模式 感觉更加注重内部对象的细节，内部零件装配的顺序。

 - ### 原型模式（prototype pattern）
   用于创建**重复**的对象，同时又能保证性能
    这种模式是实现了一个原型接口，该接口用于创建当前对象的**克隆**。当直接创建对象的代价比较大时，则采用这种模式。
    这个模式感觉一一般和工厂模式一起使用的比较多 比较方便
  
----------

 - ## 结构型模式（ctructural pattern）

    这些设计模式关注类和对象的组合。继承的概念被用来组合接口和定义组合对象获得新功能的方式。
    
- ### 适配器模式（adapter pattern）
    将一个类的接口转换成客户希望的另外一个接口。适配器模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。  
	![adapter_pattern][2]
- ### 桥接模式（bridge pattern）
    是用于把抽象化与实现化解耦，使得二者可以独立变化，目的就是为了抽象与实现分离，都可以有独立的变化，用抽象类依赖实现类来实现。  
	![bridge_pattern][3]
    
 *这里简单说一下桥接模式与适配器模式的**区别**：从名字来理解比较容易理解，适配器嘛 那就是已经有了一些功能类，但是需要一个新的功能，这个时候没必要写重复的或者说现在有可重用的功能，就需要适配器了，打个比方，你现在有一个type-c接口的手机，还有一个圆孔耳机，手机是可以没必要再“钻”一个圆孔耳机孔，而耳机也不能锤扁为type-c，这个时候type-c转接头就来了，这就是一个适配器；**但是桥接模式中**，有一个根本的区别就是**桥接模式一开始设计就是这样的架构**，是为了可扩展，而不是**设配器模式是为了解决问题而诞生的**，这是一种设计最初就存在的模式，桥接模式一般是一个抽象实体类，然后内部拥有功能接口的引用，这种结构很方便功能实现类的扩展，还有实体类的扩展。*

- ### 过滤器模式（filter pattern）/标准模式（criteria pattern）
    这个模式允许开发人员使用不同的标准过滤一组对象，通过该逻辑运算易解耦合的方式把他们连接起来，通过结合多个标准来获得一个标准。
    
- ### 组合模式（composite pattern）
    把一组相似的对象当作一个单一的对象。组合模式依据树形结构来组合对象，用来表示部分以及整体层次，创建了对象组的树形结构,创建了一个包含自己对象组的类，该类提供修改对象组的方式。
    
- ### 装饰器模式（decorator pattern）
    允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型的设计模式属于结构型模式，它是作为现有的类的一个包装。
    这种模式创建了一个装饰类，用来包装原有的类，并在保持类方法签名完整性的前提下，提供了额外的功能。
    ![decoration_pattern][4]
    
   

- ### 外观模式（facade pattern）

    该模式会隐藏系统复杂性，向现有的系统添加了一个接口。比如 电脑来讲，开启电脑的是会同时开启CPU，硬盘，内存，但是我们只管开启电脑而不需要知道其中内部的实现。
   
  
- ### 享元模式（flyweght pattern）
    为了减少创建对象的数量来减少内存的占用提高性能，是对现有的对象尝试重用，如果没找到匹配的对象，则创建新的对象。比如：在String类中，会把创建多的字符串放在字符串缓存池里边。
    **注意：**
     1.要区别出对象的内部状态与外部状态，否则容易有引起一些线程问题
     1.一般与工厂模式混合使用，便于对象的管理
- ### 代理模式（proxy pattern）
    在代理模式中，我们创建具有现有对象的对象，以便向外界提供功能接口，为其他对象提供一个代理以控制对这个对象的访问。主要是为了隐藏被代理类的内部实现。
    ![proxy_pattern][5]

    
----------


 - ## 行为型模式（behavioral pattern）    
    这些设计模式特别关注对象之间的通信
 - ### 责任链模式（chain of responsibility pattern）
    请求创建了一个接收者对象的链。这种模式给予请求的类型，对请求的发送者和接收者进行解耦在这种模式中，通常每个接收者都包含对另一个接收者的引用。如果一个对象不能处理该请求，那么它会把相同的请求传给下一个接收者，依此类推。
 - ### 命令行模式（command pattern）
    一种数据驱动的设计模式，请求以命令的形式包裹在对象中，并传给调用对象。调用对象寻找可以处理该命令的合适的对象，并把该命令传给相应的对象，该对象执行命令
 - ### 解释模式（interpreter pattern）
    提供了余元的语法或表达式的方式，属于行为模式,这种模式实现了一个表达式接口，该接口解释一个特定的上下文，一般用于SQL解析，符号处理解析引擎之类。这个模式一般比较少见。
 - ### 迭代器模式（iterator pattern）
    这个模式用于顺序访问集合对象的元素，而不需要知道底层对象的表示。
 - ### 中介者模式（mediator pattern）
    用于降低多个对象和类之间的通信复杂性。提供了中介类，这个中介类负责处理不同类之间的通信，使系统降耦合。
    适用场景：系统对象之间存在比较复杂的引用关系，导致他们之间的依赖关系结构混乱难以复用；或者想通过一个中间类封装多个类中的行为，而又不想生成太多的子类。
 - ### 观察者模式（observer pattern）
    当对象存在一对多的关系时候，比如：一个对象被修改的时候，就会通知他它的依赖对象.  
	实现方式：在抽象类里使用观察者列表。  

    **注意:** 使用的时候不要循环引用
 - ### 状态模式(state pattern )
	类的行为是根据其状态而定的，在状态模式中,通常创建的表示状态的对象和一个行为随着状态对象改变而改变的context对象。  
	允许对象在内部状态发生改变的时候改变它的行为。
 - ### 空对象模式(null object pattern)
	一个可能空对象取代null对象的例的检查，null对象不是检查空值,而是反应一个不做任何动作的关系，这样的null对象也可以在数据不可用的时候提供默认的行为。  
	空对象模式中，通常创建一个指定各种要执行的操作的抽象类和扩展该类的实体类，然后创建未对该类做任何实现的空对象类，该类可以无缝的使用在需要检查空值的地方。

 - ### 策略模式(strategy pattern)
	该模式中，一个类的行为和算法可以运行时更改.  
	一般会创建表示各种策略的对象和一个随着策略对象改变的context,策略对象改变context对象的执行  算法。  
	**注意：** 策略模式与状态模式很相似，其中主要的差别在于，策略模式有着暴露给使用者策略改变的这样的实现策略，但是状态模式中，每一个状态类里都要与一个context进行关联，从而改变状态.
	
 - ## J2EE设计模式    
    这些设计模式特别关注表示层。这些模式是由 Sun Java Center 鉴定的。
    


# 待续。。。


  [1]: https://github.com/Vincent9101/desgin-pattern
  [2]: 装饰器模式.jpg
  [3]: 适配器器模式.jpg
  [4]: 桥接模式.jpg
  [5]: ./代理模式.jpg