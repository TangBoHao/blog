---
title: Spring笔记
date: 2018-05-09 16:48:58
---

#spring中的一些基本概念

IOC： 控制反转 达到松耦合的目的
Bean 
AOP:面向切面 允许通过分离应用的业务逻辑与系统级服务进行内聚性的开发

<!--more-->

包含并管理应用对象的配置和生命周期，是一个容器
将简单的组件配置、组合为复杂的应用，是一个框架

##spring作用：
容器
提供了对多种技术的支持（JMS、MQ支持、UnitTest等）
AOP（事务管理、日志等）
提供了众多方便应用的辅助类（JDBC Template等）
对主流应用框架提供了良好的支持

##适用范围：
 - 构建企业应用
 - 单独使用bean容器
 - 单独使用AOP进行切面处理
 - 其他spring功能，如：对消息的支持等
 - 在互联网中的应用

##IOC：
###接口及面向接口编程：
接口即声明，声明了哪些方法是对外公开提供的
java8中，接口也可以有方法体
结构设计中，分清层次及调用关系，每层只向外（上层）提供一组功能接口，各层间依赖接口而非实现类。
接口的变动不影响各层间的调用。
“面向接口编程”中的接口是用于隐藏具体实现和实现多态性的组件。

###IOC概念：
控制反转，控制权的转移，应用程序本身不负责依赖对象的创建和维护，而是由外部容器负责创建和维护。DI（依赖注入）是其一种实现方式。
目的：创建对象并且组装对象之间的关系。
控制被反转之后，获得对象的过程由自身管理变为了由IOC容器主动注入。



##Spring的Bean配置：
示例：
```xml
<beans>
<bean id="oneInterface" class="com.ioc.interfaces.OneInterfaceImpl"></bean>
</beans>
```
获取Bean的方式：示例：
```java
OneInterface oonebean=usuper.getBean("oneInterface")
```





###Bean的初始化
####基础：两个包
-org.springframework.beans
-org.springframework.comtext
-BeanFactory提供配置结构和基本功能，加载并初始化Bean
-ApplicationContext保存了Bean对象并在Spring中被广泛使用

####方式：ApplicationContext
-加载本地文件
	示例：
```java
	FileSystemXmlApplicationContext context=new FileSystemXmlApplicationContext("D:/worksapce/appcontext.xml")
```
-Classpath（相对路径）

示例：
```java
	FileSystemXmlApplicationContext context=new FileSystemXmlApplicationContext("classpath:spring-context.xml")
```
-web应用中依赖servlet或listener
示例：
```xml
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderLister</listener-class>
</listener>

<servlet>
	<servlet-name>context</servlet-name>
	<servlet-class>org.springframework.web.context.ContextLoaderServlet</servlet-class>
	<load-on-startup>1</load-on-startup>
</servlet>
```

####Spring的常用注入方式:
Spring注入是指在启动Spring容器加载bean配置的时候，完成对变量的赋值行为。
常见的两种注入方式：
-设值注入
示例：
```
<beans>xml
	<bean id="injectionService" class="com.ioc.interfaces.InjectionServiceImpl">
		<property name="injectionDAO" ref="injectionDAO">
	</bean>
	<bean id=injectionDAO" class="com.ioc.injection.dao.InjectionDAOImpl"></bean>
</beans>
```


-构造注入
示例：
```xml
<beans>
	<bean id="injectionService" class="com.ioc.interfaces.InjectionServiceImpl">
		<constructor-arg name="injectionDAO" ref="injectionDAO">
	</bean>
	<bean id=injectionDAO" class="com.ioc.injection.dao.InjectionDAOImpl"></bean>
</beans>
```




