[参考 基本概念和例子](https://www.cnblogs.com/wmyskxz/p/8820371.html)  
[系列教程](https://blog.csdn.net/endlessseaofcrow/article/details/79519493 "参考 详细一点")
# Spring
## 认识Spring
- Spring 是一个轻量级的 1)DI / IoC 和 2)AOP 容器的**开源框架**
- Spring 提倡以“最少侵入”的方式来管理应用中的代码，这意味着我们可以随时安装或者卸载 Spring

适用范围：任何 Java 应用  
Spring 的根本使命：简化 Java 开发  
什么是**J2EE**：一组技术规范与指南，Spring是其中一种

### 为什么要用Spring，Spring能做什么
①.Spring 能帮我们根据配置文件创建及组装对象之间的**依赖关系**。  
②.Spring 面向切面编程能帮助我们无耦合的实现日志记录，性能统计，安全控制。  
③.Spring 能非常简单的帮我们管理数据库事务。  
④.Spring 还提供了与第三方数据访问框架（如Hibernate、JPA）无缝集成，而且自己也提供了一套JDBC访问模板来方便数据库访问。  
⑤.Spring 还提供与第三方Web（如Struts1/2、JSF）框架无缝集成，而且自己也提供了一套Spring MVC框架，来方便web层搭建。  
⑥.Spring 能方便的与Java EE（如Java Mail、任务调度）整合，与更多技术整合（比如缓存框架）

### Spring框架的结构
![](https://upload-images.jianshu.io/upload_images/7896890-a7c003d175bd41af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Spring IoC 和 DI
### 概念
#### IoC
- 读作“反转控制”，更好理解，不是什么技术，而是一种设计思想，就是将原本在程序中手动创建对象的控制权，交由Spring框架来管理。


**正控**：若要使用某个对象，需要自己去负责对象的创建  
**反控**：若要使用某个对象，只需要从 Spring 容器中获取需要使用的对象，不关心对象的创建过程，也就是把创建对象的控制权反转给了Spring框架

这就是一种控制反转的理念：**控制反转是一种通过描述（在 Java 中可以是 XML 或者注解）并通过第三方（Spring）去产生或获取特定对象的方式。**

**好处：**

- 降低对象之间的**耦合**
- 我们不需要理解一个类的具体实现，只需要知道它有什么用就好了（直接向 IoC 容器拿）

#### DI
Dependency Injection **依赖注入**，在Spring框架负责创建Bean对象时，动态的将依赖对象注入到Bean组件。  

**实现IoC思想需要DI做实现** （重要）

- **注入方式**（为一个bean设置属性）： set方法注入；构造方法注入；字段注入  
- 注入类型：值类型；引用类型

### Spring IoC 容器
**IoC 容器**来管理和容纳我们所开发的各种各样的 Bean，并且我们可以从中获取各种发布在 Spring IoC 容器里的 Bean，并且通过描述可以得到它。

**Spring的核心类**：

- BeanFactory
- ApplicationContext

#### 对比BeanFactory和ApplicationContext
- 都是用来提供IoC的功能，负责配置，创建和管理Bean
- 总的来说，ApplicationContext接口继承BeanFactory接口，方法更多一些，也更推荐使用
- 除了拥有BeanFactory的功能之外，ApplicationContext 还提供了以下功能：① 支持国际化；  ② 支持消息机制；  ③ 支持统一的资源加载；  ④ 支持AOP功能；  

### bean的生命周期和作用域
``<bean name="user" class="com.sjtu.bean.User" scope="singleton" init-method="init" destroy-method="destroy"></bean>``
#### 生命周期 
定义--->初始化--->调用--->销毁

##### 定义 
- Resource 定位
Spring IoC 容器先根据开发者的配置，进行资源的定位，在 Spring 的开发中，通过 XML 或者注解都是十分常见的方式，定位的内容是由开发者提供的。
- BeanDefinition 的载入
这个时候只是将 Resource 定位到的信息，保存到 Bean 定义（BeanDefinition）中，此时并不会创建 Bean 的实例
- BeanDefinition 的注册
这个过程就是将 BeanDefinition 的信息发布到 Spring IoC 容器中
注意：此时仍然没有对应的 Bean 的实例。

#####初始化
- A.在配置文件中通过指定``init-method``属性来完成
- B.实现org.springframwork.beans.factory.InitializingBean接口

##### bean的实例化方式：（重要）
- 使用类构造器实例化(默认无参数) // 最常用的，简单的
- 使用静态工厂方法实例化(简单工厂模式)
- 使用实例工厂方法实例化(工厂方法模式)

##### bean销毁
- A.使用配置文件指定的``destroy-method``属性
- B.实现org.springframwork.bean.factory.DisposeableBean接口

#### 作用域（scope属性）

- singleton 单例对象  
当一个bean的作用域为singleton, 那么Spring IoC容器中只会存在一个共享的bean实例，并且所有对bean的请求，只要id与该bean定义相匹配，则只会返回bean的同一实例。
- prototype 多例原型  
Prototype作用域的bean会导致在每次对该bean请求（将其注入到另一个bean中，或者以程序的方式调用容器的getBean() 方法）时都会创建一个新的bean实例。根据经验，对所有有状态的bean应该使用prototype作用域，而对无状态的bean则应该使用 singleton作用域
- request web环境下  
在一次HTTP请求中，一个bean定义对应一个实例；即每次HTTP请求将会有各自的bean实例， 它们依据某个bean定义创建而成。该作用 域仅在基于web的Spring ApplicationContext情形下有效。
- session web环境下  
在一个HTTP Session中，一个bean定义对应一个实例。该作用域仅在基于web的Spring ApplicationContext情形下有效。
- global session  
在一个全局的HTTP Session中，一个bean定义对应一个实例。典型情况下，仅在使用portlet context的时候有效。该作用域仅在基于 web的Spring ApplicationContext情形下有效。

## AOP
**AOP 即 Aspect Oriented Program 面向切面编程**  
是Spring的重要功能之一，在数据库事务中被广泛使用

在面向切面编程AOP的思想里面，核心业务功能和切面功能分别独立进行开发，然后把**切面功能**和**核心业务**功能 "**编织**" 在一起，这就叫AOP

所谓的**核心业务**，比如登陆，增加数据，删除数据都叫核心业务；所谓的**周边功能**，比如性能统计，日志，事务管理等等

**AOP 的目的**：  
AOP能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。

### AOP中的概念
- 切面（Aspect）：一个关注点的模块化，这个关注点可能会横切多个对象。事务管理是J2EE应用中一个关于横切关注点的很好的例子。 在Spring AOP中，切面可以使用通用类（基于模式的风格） 或者在普通类中以 @Aspect 注解（@AspectJ风格）来实现。

- 连接点（Joinpoint）：在程序执行过程中某个特定的点，比如某方法调用的时候或者处理异常的时候。 在Spring AOP中，**一个连接点 总是 代表一个方法的执行**。 通过声明一个org.aspectj.lang.JoinPoint类型的参数可以使通知（Advice）的主体部分获得连接点信息。

- 通知（Advice）：**在切面的某个特定的连接点（Joinpoint）上执行的动作**。通知有各种类型，其中包括“around”、“before”和“after”等通知。 通知的类型将在后面部分进行讨论。许多AOP框架，包括Spring，都是以拦截器做通知模型， 并维护一个以连接点为中心的拦截器链。

- 切入点（Pointcut）：匹配连接点（Joinpoint）的断言。通知和一个切入点表达式关联，并在满足这个切入点的连接点上运行（例如，当执行某个特定名称的方法时）。 **切入点表达式如何和连接点匹配是AOP的核心**：Spring缺省使用AspectJ切入点语法。

- 引入（Introduction）：（也被称为内部类型声明（inter-type declaration））。声明额外的方法或者某个类型的字段。 Spring允许引入新的接口（以及一个对应的实现）到任何被代理的对象。例如，你可以使用一个引入来使bean实现 IsModified 接口，以便简化缓存机制。

- 目标对象（Target Object）： 被一个或者多个切面（aspect）所通知（advise）的对象。也有人把它叫做 被通知（advised） 对象。 既然Spring AOP是通过运行时代理实现的，这个对象永远是一个 被代理（proxied） 对象。

- AOP代理（AOP Proxy）： AOP框架创建的对象，用来实现切面契约（aspect contract）（包括通知方法执行等功能）。 在Spring中，AOP代理可以是JDK动态代理或者CGLIB代理。 注意：Spring 2.0最新引入的基于模式（schema-based）风格和@AspectJ注解风格的切面声明，对于使用这些风格的用户来说，代理的创建是透明的。

- 织入（Weaving）：把切面（aspect）连接到其它的应用程序类型或者对象上，并创建一个被通知（advised）的对象。 这些可以在编译时（例如使用AspectJ编译器），类加载时和运行时完成。 Spring和其他纯Java AOP框架一样，在运行时完成织入。

#### 通知的类型 （重要）
- **前置通知** 目标方法之前调用
- **后置通知** （如果出现异常不会调用） 在目标方法运行之后
- **环绕通知** 在目标方法之前和之后都调用
- **异常拦截通知** 如果出现异常，就会调用
- **后置通知** （无论是否出现异常都会调用） 在目标方法运行之后调用

## 配置AOP
两种配置方式：（注解用的多一点）

- XML配置
- 注解配置

### 使用注解来开发 Spring AOP
1. 开发连接点
	- 我们需要增强的类的方法（被拦截的类） 
2. 创建切面，定义切点
	- ``@Aspect``注解一个类 
	- ``@Pointcut``注解切入点
	- 还要注解通知类型
3. 配置到XML中
4. 测试AOP

aspect:

	package aspect;
	
	import org.aspectj.lang.annotation.After;
	import org.aspectj.lang.annotation.Aspect;
	import org.aspectj.lang.annotation.Before;
	import org.aspectj.lang.annotation.Pointcut;
	import org.springframework.stereotype.Component;
	
	@Component
	@Aspect
	class Broker {
	
	    @Pointcut("execution(* pojo.Landlord.service())")
	    public void lService() {
	    }
	
	    @Before("lService()")
	    public void before() {
	        System.out.println("带租客看房");
	        System.out.println("谈价格");
	    }
	
	    @After("lService()")
	    public void after() {
	        System.out.println("交钥匙");
	    }
	}

xml：配置自动注入，并告诉 Spring IoC 容器去哪里扫描这两个 Bean：

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xmlns:aop="http://www.springframework.org/schema/aop"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
	       http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
	
	    <context:component-scan base-package="aspect" />
	    <context:component-scan base-package="pojo" />
	
	    <aop:aspectj-autoproxy/>
	</beans>
	
	<!--或者-->
	<!-- 1.配置目标对象 -->
    <bean name="userService" class="com.sjtu.proxy.UserServiceImpl"></bean>
	<!-- 2.配置通知对象 -->
    <bean name="myAdvice" class="com.sjtu.proxy.MyAdvice"></bean>   
	<!-- 3.注解配置 -->
	<aop:aspectj-autoproxy/>


# 问题
- Spring的核心类有哪些：BeanFactory; ApplicationContext; BeanWrapper
- 什么是IoC和DI，区别是
- 比较BeanFactory接口和ApplicationContext接口
- Spring配置bean实例化方法
- Bean如何注入属性
- Spring生命周期(???)
- Spring中bean的生命周期和作用域
- 基于XML配置Spring，基于Java配置Spring(???)
- 什么是AOP，AOP内几个名词的解释
- 通知有哪些类型
- 介绍一下Spring的事物管理
- Spring里面如何配置数据库驱动？
- Spring里面如何定义hibernate mapping？
- Spring如何处理线程并发问题？

## bean设置属性的方法
![](http://images2015.cnblogs.com/blog/799093/201607/799093-20160724232926263-388770666.png)

其中： 

- 构造器注入，通过 <constructor-arg> 元素完成注入
- setter方法注入， 通过<property> 元素完成注入【开发中常用方式】

## 介绍一下Spring的事物管理
事务就是对一系列的数据库操作（比如插入多条数据）进行统一的提交或回滚操作，如果插入成功，那么一起成功，如果中间有一条出现异常，那么回滚之前的所有操作。这样可以防止出现脏数据，防止数据库数据出现问题。

开发中为了避免这种情况一般都会进行事务管理。Spring中也有自己的事务管理机制，一般是使用**TransactionMananger**进行管 理，可以通过Spring的注入来完成此功能。spring提供了几个关于事务处理的类：

- TransactionDefinition //事务属性定义
- TranscationStatus //代表了当前的事务，可以提交，回滚。
- **PlatformTransactionManager这个是spring提供的用于管理事务的基础接口**，其下有一个实现的抽象类 AbstractPlatformTransactionManager,我们使用的事务管理类例如 DataSourceTransactionManager等都是这个类的子类。

一般事务定义步骤：

	TransactionDefinition td =newTransactionDefinition();
	TransactionStatus ts = transactionManager.getTransaction(td);
	try{ 
	    //do sth
	    transactionManager.commit(ts);
	}catch(Exception e){
	    transactionManager.rollback(ts);
	}
 spring提供的事务管理可以分为两类：编程式的和声明式的。  
 编程式的，比较灵活，但是代码量大，存在重复的代码比较多；声明式的比编程式的更灵活。
 

**编程式主要使用transactionTemplate**。省略了部分的提交，回滚，一系列的事务对象定义，需注入事务管理对象.
	void add(){
	    transactionTemplate.execute(newTransactionCallback(){
	        pulic Object doInTransaction(TransactionStatus ts){
	         //do sth
	        }
	    }
	}
	
**声明式使用TransactionProxyFactoryBean:PROPAGATION_REQUIRED PROPAGATION_REQUIRED PROPAGATION_REQUIRED,readOnly  
围绕Poxy的动态代理 能够自动的提交和回滚事务**

``org.springframework.transaction.interceptor.TransactionProxyFactoryBean``

- PROPAGATION_REQUIRED–支持当前事务，如果当前没有事务，就新建一个事务。这是最常见的选择。
- PROPAGATION_SUPPORTS–支持当前事务，如果当前没有事务，就以非事务方式执行。
- PROPAGATION_MANDATORY–支持当前事务，如果当前没有事务，就抛出异常。
- PROPAGATION_REQUIRES_NEW–新建事务，如果当前存在事务，把当前事务挂起。
- PROPAGATION_NOT_SUPPORTED–以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
- PROPAGATION_NEVER–以非事务方式执行，如果当前存在事务，则抛出异常。
- PROPAGATION_NESTED–如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则进行与 PROPAGATION_REQUIRED类似的操作。

## Spring如何处理线程并发问题？
Spring使用ThreadLocal解决线程安全问题

我们知道在一般情况下，只有无状态的Bean才可以在多线程环境下共享，在Spring中，绝大部分Bean都可以声明为singleton作用域。就是因为Spring对一些Bean(如RequestContextHolder、TransactionSynchronizationManager、LocaleContextHolder等)中非线程安全状态采用ThreadLocal进行处理，让它们也成为线程安全的状态，因为有状态的Bean就可以在多线程中共享了。

ThreadLocal和线程同步机制都是为了解决多线程中相同变量的访问冲突问题。

在同步机制中，通过对象的锁机制保证同一时间只有一个线程访问变量。这时该变量是多个线程共享的，使用同步机制要求程序慎密地分析什么时候对变量进行读写，什么时候需要锁定某个对象，什么时候释放对象锁等繁杂的问题，程序设计和编写难度相对较大。

而ThreadLocal则从另一个角度来解决多线程的并发访问。ThreadLocal会为每一个线程提供一个独立的变量副本，从而隔离了多个线程对数据的访问冲突。因为每一个线程都拥有自己的变量副本，从而也就没有必要对该变量进行同步了。ThreadLocal提供了线程安全的共享对象，在编写多线程代码时，可以把不安全的变量封装进ThreadLocal。

由于ThreadLocal中可以持有任何类型的对象，低版本JDK所提供的get()返回的是Object对象，需要强制类型转换。但JDK5.0通过泛型很好的解决了这个问题，在一定程度地简化ThreadLocal的使用。

概括起来说，对于多线程资源共享的问题，同步机制采用了“以时间换空间”的方式，而ThreadLocal采用了“以空间换时间”的方式。前者仅提供一份变量，让不同的线程排队访问，而后者为每一个线程都提供了一份变量，因此可以同时访问而互不影响。

## Spring里面如何配置数据库驱动？
使用``org.springframework.jdbc.datasource.DriverManagerDataSource`` 数据源来配置数据库驱动。示例如下：

	<bean id=”dataSource”> 
	    <property name=”driverClassName”> 
	        <value>org.hsqldb.jdbcDriver</value>
	    </property> 
	    <property name=”url”> 
	        <value>jdbc:hsqldb:db/appfuse</value> 
	    </property> 
	    <property name=”username”><value>abc</value></property> 
	    <property name=”password”><value>abc</value></property> 
	</bean> 