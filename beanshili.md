---
title: Spring中bean的实例化
date: 2018-06-04 01:23:01
tags:
---
#bean实例化的方式

##在spring里面通过配置文件创建对象

###bean实例化的三种方式
1. 使用类的无参数构造
2. 使用静态工厂创建
创建静态的方法 返回类对象
class值为工厂的类 factory-method的值为生成对象的方法
示例：
```
<bean id="user" class="com.Factory" factory-method="getUser"></bean>
```
<!--more-->

3. 使用实例工厂创建
创建不是静态的方法 返回类对象
先在xml中声明工厂bean，然后声明bean的工厂bean和工厂方法。
示例：
```
<bean id="userFactory" class="com.Factory" ></bean>
<bean id="user" factory-bean="userFactory" factory-method="getUser"></bean>
```

###bean中常用的属性
- id
- class
- name 作用类似id 但是可以包含特殊符号
- scope：singleton 单例的 prototype多例的

#属性注入
1. 使用set方法注入
示例：
```
 	<bean id="user" class="com.User">
    <property name="username" value="kate"></property>
    </bean>
```
2. 有参数的构造来注入
示例：
```
	<bean id="user" class="com.User">
    <!--使用有参构造方法注入-->
    <constructor-arg name="username" value="kally"></constructor-arg>
    </bean>
```


 ##注入对象类型的属性
```
    <bean id="user" class="com.User">
    <property name="username" value="kate"></property>
    </bean>
    
    <bean id="server" class="com.UserServer">
    <property name="obj" ref="user"></property>
    </bean>

```

##IOC和DI的区别
IOC：控制反转，把对象创建交给spring进行配置。
DI：依赖注入，向类里面的属性设置值。

关系：依赖注入不能单独存在，需要在IOC的基础上完成操作。

##加载项目启动前加载配置文件
原理：
1. ServletConetext对象
2. 监听器

在服务器启动的时候，为每个项目创建一个ServletConext对象，使用监听器，可以监听到ServletContext对象在什么时候创建。
当ServletContext对象创建的时候，把配置文件的对象创建。









