---
title: java设计模式
date: 2018-08-23 14:45:47
tags: java
catagories: article
cover: img/cover3.jpg
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

一个实体应当尽量少地与其他实体之间发生相互作用，使得系统功能模块相对独立。v

**6. 合成复用原则（Composite Reuse Principle）**
尽量使用合成/聚合的方式，而不是使用继承。



# 设计模式可以分为三大类：

 - ## 创建型模式（creational pattern）
   隐藏创建对象的创建逻辑，提供创建对象的接口，而非使用new关键字进行创建。

 - ### 工厂模式（factory pattern）

    隐藏创建的对象的逻辑，通过共同对的接口创建对象。
    实现demo结构如下图,实现demo代码  
	[点这里：这是我学习的时候写的demo](https://github.com/Vincent9101/Record/tree/design_pattern)   
	
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
 - ### 模板模式（template pattern）  
	一个抽象的类公开定义其执行它的方法的方式模板，其子类需要重写方法实现，但是调用以抽象类中定义的方式调用。  
	优点：封装不变的部分，扩招可变部分，提取公共代码便于维护。实现起来十分简单。  
 - ### 访问者模式(vistor pattern)  
	使用了一个访问者类，它改变了元素类的执行算法。通过这种方式，元素的执行算法可以随着访问者改变而改变，在需要对一个对象结构中的对象进行很多不同的并且不相关的操作，并且避免让这些操作"污染"这些对象的类，就可以使用访问者模式将这些封装到类中。 
	目的：为了将数据结构与数据操作分离。
  
	
 - ## J2EE设计模式    
    这些设计模式特别关注表示层。这些模式是由 Sun Java Center 鉴定的。  
 - ### MVC模式(Model-View-Cotroller)  
    Model:模型代表一个存储数据的对象，可以带有逻辑处理，用于数据变化的时候更新控制器。  
	View:视图代表模式包含的数据可视化。  
	Controller：控制器用于模型和视图上。控制数据流向模型对象，并且在数据变化的时候更新视图，使模型与视图分离开。
 - ### 业务代表模式(business delegate pattern) 
	用于业务层与表示层解耦。用来减少通信或对表示层代码中的业务层代码的远程查询功能。  
	该模式有四个模块：  
	- 客户端(client) ：表示层，用户端展示部分代码,交互响应的部分。  
	- 业务代表(business delegate):作为客户端请求服务的委托，提供对业务方法的访问，可以理解为代理执行服务的类，提供客户都但访问的接口。  
	- 查询服务(lookup service): 查询服务对象负责获取相关的业务实现，可以理解为“业务对象的工厂类”，然后提供服务对象。
    - 业务接口（business service）：各种服务的抽象声明，便于业务事项的扩展与分类整合，为查询服务提供服务对象，实现该接口的类作为服务实现类，实现具体业务逻辑。   
 - ### 组合实体模式(composite entity pattern)  
	用在 EJB 持久化机制中。一个组合实体是一个 EJB 实体 bean，代表了对象的图解。当更新一个组合实体时，内部依赖对象 beans 会自动更新，因为它们是由 EJB 实体 bean 管理的。以下是组合实体 bean 的参与者:  
	- 组合实体（composite entity):主要的实体 bean。它可以是粗粒的，或者可以包含一个粗粒度对象，用于持续生命周期.  
	- 粗粒度对象（coarse-grained object):该对象包含依赖对象，拥有自己的生命周期，可以管理依赖对象的声明周期。
	- 依赖对象（dependent object）:依赖对象是一个持续声明周期依赖于粗粒度对象的对象。  
	- 策略（strategies）：描述如何实现组合实体  
 - ### 数据访问对象模式（data access object pattern)  
    数据访问对象模式即DAO模式，用于把低级的数据访问API操作从高级的业务服务中分离出来。  
	- 数据访问对象接口（data access object interface）定义在模型对象需要执行的操作标准。
	- 数据访问对象实体类（data access object concrete class）用于实现上述接口,负责从数据源头中获取数据.  
	- 模型对象/数值对象（model object)  
	简单POJO对象，拥有get/set存储设置通过DAO类检索到的数据。  
 - ### 前端控制器模式(front controller pattern)  
	用来提供集中的请求控制处理机制，所有的请求都将由一个单一的处理程序处理。该程序可以做认证/授权/记录日志，或者跟踪请求，然后把请求转给相应的处理程序。  
	- 前端控制器（front controller）处理应用程序所有的类型请求的单个处理程序，应用程序可以基于web，可以基于桌面。  
	- 调度器（dispatcher） 
	前端控制器可能使用一个调度器对象来调度请求对应的具体处理程序。  
	- 视图（view)  
	视图是为请求而创建的对象。
 - ### 拦截过滤器模式（intercepting filter pattern)
	用于对应用程序的请求或者响应做一些预处理或者说是后处理,定义过滤器，并在把请求传给实际目标应用程序之前应用在请求上。  
	- 过滤器（filter）过滤器在请求处理程序之前或者之后，执行某些任务。  
	- 过滤器链（filter chain）过滤器链带有多个过滤器，并在Target上按定义的顺序执行过滤器。
	- Target  
	Target对象是请求处理程序  
	过滤管理器（filter manager）  
	过滤管理器用于管理过滤和过滤器链。  
	客户端（client）  
	client是向Target对象发送请求的对象。   
 - ### 服务器定位器模式(service locator pattern)  
	当使用JNDI查询定位各种服务的时候，若查找JNDI服务的代价很高，服务定位器模式充分利用了，缓存技术。在首次启用某一个服务的时候,服务定位器会在JNDI中查找服务，并且缓存在服务对象里边，当再次请求相同的服务的时候，会从缓存中寻找该服务。  
	- 服务(service)： 实际处理请求的服务，对这种服务的引用可以在JNDI服务器中查找到。  
	- context/初始的context：JNDI context带有对要查找的服务的引用。  
	- 服务器定位器(service locator)：服务定位器是通过JNDI查找缓存服务来获取服务的单点接触。  
	- 缓存(cache)：缓存存储服务的引用，以便复用它们。  
	- 客户端(client)：该对象通过service locator调用服务对象。  
 - ### 传输对象模式(transfer object pattern)  
	用于从客户端想服务器一次性传递带有多个属性的数据。传输对象又被称为数值对象，是具有getter与setter的的POJO类，可序列化，没有任何的行为。服务器端的业务类从数据库中读取数据填充POJO发送给客户端使用。对于客户端该对象是只读的，用户可以创建自己的传输对象传输给服务器，用于更新数据中的数值。  
	- 业务对象(business object): 为传输对象填充数据的业务服务。  
	- 传输对象(transfer object)：简单的POJO，只有getter与setter用来设置获取属性数据。  
	- 客户端(client) ： 用来发送请求或者发送传输对象到业务对象。 

	


	

# 待续。


  [4]: 装饰器模式.jpg
  [2]: 适配器器模式.jpg
  [3]: 桥接模式.jpg
  [5]: 代理模式.jpg