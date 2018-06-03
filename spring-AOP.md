---
title: spring-AOP
date: 2018-05-09 18:32:13
---
##AOP概念及实现方式
概念：面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。

主要的功能是：

 - 日志记录
 - 性能统计
 - 安全控制
 - 事务处理
 - 异常处理等
<!--more-->

实现方式：
预编译：
-AspectJ
运行期动态代理（JDK动态代理、CGLib动态代理）
-SpringAOP、JbossAOP

###AOP中几个相关的概念
 - 切面：一个关注点的模块化，这个关注点可能回横切多个对象
 - 连接点：程序执行过程中某个特定的点
 - 通知：在切面的某个特定的连接点上执行的动作
 - 切入点：匹配连接点的断言，在AOP中通知和一个切入点的表达式关联。
 - 引入：在不修改类代码的前提下，为类添加新的方法和属性。
 - 目标对象：被一个或多个切面所通知的对象。
 - AOP代理；AOP框架创建的对象，用来实现切面契约(包括通知方法执行等功能)。
 - 织入：把切面连接到其他的应用程序类型或对象上，并创建一个被通知的对象，分为：编译时织入、类加载时织入、执行时织入。

###Advice(通知)的类型：
 - 前置通知：在连接点之前执行的通知，但不能组织连接点前的执行（除非它抛出一个异常）。
 - 返回后通知：在连接点正常完成后执行的通知。
 - 抛出异常后通知：在方法抛出异常退出时执行的通知。
 - 后通知：当连接带你退出的时候执行的通知（不论是正常返回还是异常退出）。
 - 环绕通知：包围一个连接点的通知。




###Spring中的AOP
用途：
提供了声明式的企业服务，特别是EJB的替代服务的声明。
允许用户定制自己的方面，以完成OOP与AOP的互补使用。

SPring中AOP的实现：

 - 纯java实现，无需特殊的编译过程，不需要控制类加载器的层次。
 - 目前只支持方法执行连接点（通知Spring Bean的方法执行）。
 - 不是为了提供最完整的AOP实现，而是侧重于提供一种AOP实现和Spring IoC容器之间的正和，用于帮助解决企业应用中常见的问题。
 - Spring AOP不会与AspectJ竞争，从而提供综合全面的AOP解决方案。

###有接口和无接口的Spring AOP实现区别
 - Spring AOP默认使用标准的JavaSE动态代理作为AOP代理，这使得任何接口（或者接口集）都可以被代理
 - Spring AOP中也可以使用CGLIB代理（如果一个业务对象并没有实现一个接口）。

###Schema-based AOP
Spring所有的切面和通知器都必须放在一个<aop:config>内（可以配置包含多个<aop:config>元素），每一个<aop:config>可以包含pointcut,advisor和aspect元素。（它们必须按照这个顺序进行声明）
<apo:config>风格的配置大量使用了Spring的自动代理机制。
示例：
```xml
<aop:config>
	<aop:aspect id="myaspect" ref="aBean">
	...
	</aop:aspect>
</aop:config>

<bean id="aBean" class="...">
	...
</bean>
```
把这个bean作为这个切面来配置


###Pointcut //切入点
 - execution(public**(..))	切入点为执行所有public方法时
 - execution(*set*(..))		切入点为执行所有set开始的方法时
 - execution(*com.xyz.service.AccountService.*(..))	切入点为执行AccountService类中的所有方法时
 - execution(*com.xyz.service..(..))	切入点为执行com.xyz.service包下的所有方法时
 - execution(*com.xyz.service...(..))	切入点为执行com.xyz.service包及其子包下的所有方法时
 - target(com.xyz.service.AccountService)		target用于匹配当前目标对象类型的执行方法

 - args(java.io.Serializable)
@target(org.springframework.transaction.annotation.Transaction)
@within(org.springframework.transaction.annotation.Transaction)
@annotation(org.springframework.transaction.annotation.Transaction)
等等
 > args用于匹配当前执行的方法传入的参数为指定类型的执行方法


声明示例：
```xml
<aop:config>
	<aop:aspect id="myaspect" ref="aBean">
	<aop:pointcut id="businessService" expression="execution(* com.xyz.myapp.service..(..))"/>
	<aop:aspect>
</aop:config>
```

###advice 通知
before advice:
```xml
<aop:aspect id="beforeExample" ref="aBean">
	<aop:before pointcut-ref="dataAccessOperation" method="doAccessCheck" />
</aop:aspect>

<aop:aspect id="beforeExample" ref="aBean">
	<aop:before pointcut="execution(* com.xyz.myapp.dao..(..))" method="doAccessCheck" />
</aop:aspect>
```



