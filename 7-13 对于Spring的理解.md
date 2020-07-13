---
title: Spring专题
tags: Spring
abbrlink: 20909
date: 2018-09-16 00:06:10
---
# 1 Spring 简介
[Spring](http://spring.io/) 是一个轻量级的Java 开发框架，简单来说就是管理 `Java Bean` 的轻量级容器，解决项目中Bean相互依赖的问题。
 
# 2 IOC
2.1 初步理解IOC
>* Spring主要是管理Bean的超级工厂，IOC简单说就是就是解决Bean与Bean之间的各种依赖。
 
2.2 创建Bean的2种方式
>* 注解： 通过在类上打 `@Service` `@Component` ...等几个注解,可以让当前的Bean加入spring工厂
>* xml配置： 在Spring的配置文件中
```
<bean id="testService" class="java.service.TestService">
            //这里可以初始化各个操作
</bean>
```
 
2.3 Spring 容器
 
>* `Beanfactory`, Spring容器最基本的接口就是 Beanfactory。 BeanFactory负责配置、创建、管理Bean，它有一个子接口: ApplictionContext。
>* `Applicationcontext`，大部分时候，都不会使用 Beanfactory实例作为 Spring容器，而是使用 Applicationcontext实例为容器，因此也把 Spring容器称为 `Spring上下文`。 Applicationcontext是 Beanfactory接口的子接口它増强了 BeanFactory的功能
 
 
 
# 3 AOP
3.1 下面是我对关于面向切面编程的一些简单阐述(不官方)。
>* 切面（ Aspect ）: 切面即我们定义处理公共代码的切面。
>* 连接点（ Joinpoint）:程序执行过程中明确的点，如方法的调用，或者异常的抛出。在 Spring AOP中，连接点总是方法的调用。
>* 增强处理（ Advice）:即我们定义的切入点方法，比如Before、Around...
>* 切入点（ Pointcut）:即我们定义的切入条件，比如 @Before(`"execution(*test(..))"`)
>* 引入 :将方法或字段添加到被处理的类中。 Spring允许将新的接口引入到任何被处理的对象中例如，你可以使用一个引入，使任何对象实现 Ismodified接口，以此来简化缓存。
>* 目标对象 :被AOP框架进行增强处理的对象，也被称为被增强的对象。如果AOP框架采用的是动态AOP实现，那么该对象就是一个被代理的对象。
>* AOP代理 `(原理)` :AOP框架创建的对象，简单地说，代理就是对目标对象的加强。 Spring中的AOP代理可以是`JDK动态代理`，也可以是 `cglib代理`。前者为实现接口的目标对象的代理，后者为不实现接口的目标对象的代理。
关于两个代理的区别,可以参考 [JDK动态代理和cglib代理的区别](https://blog.csdn.net/u013126379/article/details/52121096)
>* 织入（ Weaving）：将增强处理添加到目标对象中，并创建一个被增强的对象（AOP代理）的过程就是织入。织入有两种实现方式一编译时增强（如 Aspectj）和运行时增强（如 SpringAOP）。 Spring和其他纯 Java AOP框架一样，在运行时完成织入。
 
3.2 了解AOP之前,先了解java的 `AspectJ` 相关概念,可以说`AOP`也是根据`AspectJ` 开发的
3.3 关于AOP的概述,网上描述太多,笔者就不用重复介绍,这里引用一篇比较详细的博客 [Spring AOP源码解析](https://blog.csdn.net/javazejian/article/details/56267036)
3.4 个人总结:
> 1. AOP 就是将各个业务中,有相同的业务抽取出来共同处理,让开发人员只需关注实际 `业务代码`,从而达到 `解耦操作`
> 2. AOP 的实现原理就是先创建出`代理类`,然后执行我们的定义的切面方法,然后执行真正需要调用的目标方法,最后将这个`代理类`的结果返回
> 3. 执行具体的业务逻辑,可以断点查看,总体源码逻辑不是很复杂。 `断点方法` ：在调用目标方法的时候打一个断点，然后跟进即可
> 4. `注意` springboot默认使用的是 `cglib代理` 代码，如果需要关闭默认选项，让spring动态选择的话，需要在 application.properties 中新增 `pring.aop.proxy-target-class=false`
 
# 4 Spring事务
4.1 事务的几个特性: (`ACID`)
> 原子性(Atomicity)
> 一致性(Consistency)
> 隔离性(Isolation)
> 持久性(Durability)
 
 
4.2 Spring事务
事务主要是用于描述数据库的访问，Spring本身是不具备事务功能。但是Spring是通过PlatformTransactionManager接口去管理事务的，具体的共有5个实现类，分别在不同的场景下完成具体的功能
>`注意` : PlatformTransactionManager 是在 spring-tx包中，spring-tx这个包又被spring-jdbc依赖，可见Spring事务是管理jdbc连接。
 
作用 | 实现类
---- | ---
JDBC | DataSourceTransactionManager
JPA | JapTransactionManager
Hibernate | HibernateTransactionManager
JDO | JdoTransactionManager
分布式事务 | JtaTransactionManager
 
 
4.3 Spring事务的传播性
> `事务传播性` ： 是指在同一个事务操作中，如果该操作调用了其他方法，怎么去管理这多个方法的事务。可以定位到 `org.springframework.transaction.TransactionDefinition` 类中查看更具体的api注释
 
名称 | 中文翻译（非官方，便于记忆） | 值 | 作用
---- | --- | --- | ---
PROPAGATION_REQUIRED | 需要传播 | 0 | 传播当前事务到被调用方法；如果别调用方方法没有事务，则新建事务。 默认
PROPAGATION_SUPPORTS | 支持传播 | 1 | 如果被调用方有事务，就执行事务；如果被调用方没有事务，则被调用方不加事务
PROPAGATION_MANDATORY | 强制传播 | 2 | 强制被调用方方法新增事务,如果被调用方法不存在事务,则抛异常
PROPAGATION_REQUIRES_NEW | 创建新的事务 | 3 | 被调用方法 新建事务，如果当前方法存在事务，把当前事务挂起。<br>新建的事务将和被挂起的事务没有任何关系，是两个独立的事务<br>，外层事务失败回滚之后，不能回滚内层事务执行的结果，内层事务失败抛出异常，<br>外层事务捕获，也可以不处理回滚操作
PROPAGATION_NOT_SUPPORTED | 不传播事务 | 4 | 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
PROPAGATION_NEVER | 不能用事务 | 5 | 以非事务方式执行，如果当前存在事务，则抛出异常。
PROPAGATION_NESTED | 嵌套事务 | 6 | 如果被调用方方法存在事务，则运行在一个嵌套于已存在的事务中；<br>如果被调用方法没有事务，则按第一行默认的方法执行。<br>它使用了一个单独的事务，这个事务拥有多个可以回滚的保存点。<br>内部事务的回滚不会对外部事务造成影响。<br>它只对DataSourceTransactionManager事务管理器起效。
 
4.4 数据库的隔离级别
  隔离级别 | 脏读（Dirty Read ） | 不可重复读（NonRepeatable Read） | 幻读（Phantom Read）
--- | --- | --- | ---
未提交读（Read uncommitted） | 可能 | 可能 | 可能
已提交读（Read committed） | 不可能 | 可能 | 可能
可重复读（Repeatable read） | 不可能 | 不可能 | 可能
可串行化（Serializable ） | 不可能 | 不可能 | 不可能
 
>`脏读`：一事务对数据进行了增删改，但未提交，另一事务可以读取到未提交的数据。如果第一个事务这时候回滚了，那么第二个事务就读到了脏数据。
>`不可重复读`：一个事务中发生了两次读操作，第一次读操作和第二次操作之间，另外一个事务对数据进行了修改，这时候两次读取的数据是不一致的。
>`幻读`：第一个事务对一定范围的数据进行批量修改，第二个事务在这个范围增加一条数据，这时候第一个事务就会丢失对新增数据的修改。
 
<b>总结</b>
> 隔离级别越高，越能保证数据的完整性和一致性，但是对并发性能的影响也越大。
> 大多数的数据库默认隔离级别为 Read Commited，比如 SqlServer、Oracle
> 少数数据库默认隔离级别为：Repeatable Read 比如： MySQL InnoDB
 
4.5 Spring的隔离级别
>Spring的隔离级别同样在 `TransactionDefinition`类中定义的
 
名称 | 效果
--- | ---
ISOLATION_DEFAULT | 这是个 PlatfromTransactionManager 默认的隔离级别，使用数据库默认的事务隔离级别。<br>另外四个与 JDBC 的隔离级别相对应。
ISOLATION_READ_UNCOMMITTED | 这是事务最低的隔离级别，它充许另外一个事务可以看到这个事务未提交的数据。<br>这种隔离级别会产生脏读，不可重复读和幻像读。
ISOLATION_READ_COMMITTED | 保证一个事务修改的数据提交后才能被另外一个事务读取。<br>另外一个事务不能读取该事务未提交的数据。
ISOLATION_REPEATABLE_READ | 这种事务隔离级别可以防止脏读，不可重复读。但是可能出现幻像读。
ISOLATION_SERIALIZABLE | 这是花费最高代价但是最可靠的事务隔离级别。事务被处理为顺序执行。
 
# 5 Spring其他功能
5.1 指定Bean的 `作用域`
```
@Service
//这里指定bean 的作用域,默认是 singleton
@Scope("prototype")
public class TestService {
}
```
5.2 定制`生命周期行为`
```
@Service
public class TestService {
    @PostConstruct
    void init() {
        // 指定该Bean的初始化操作,Spring会在该Bean的的依赖注入完成之后回调本方法, 一般用于初始化功能
    }
    @PreDestroy
    void close() {
        //指定该Bean的销毁方法,Spring会在该Bean销毁之前回调该方法
    }
}
 
```
 
 
# 6 源码分析
 
 