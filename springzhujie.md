---
title: spring使用注解方式整合web项目
date: 2018-06-09 15:35:39
tags:
---
##spring整合web项目-注解方式
1.加载spring核心配置文件

 - new对象，效率很低

2.实现思想：把加载配置文件和创建对象的过程，放到服务器启动的时候

3.实现原理

 - ServlteContext对象
 - 监听器
 - 具体过程
  - 在服务器启动的时候，为每个项目创建一个ServletContext对象
  - 在ServletContext对象创建的时候，使用个监听器，加载spring配置文件，把配置文件配置对象创建。
  - 把创建出来的对象放到ServletContext域对象里面。（setAttribute方法）

<!--more-->
##spring中使用注解方式进行bean管理
1.注解概念
注解：代码中特殊的标记，使用注解可以完成特殊的功能。
注解写法：@注解名称(属性名称=属性值)
注解可以使用在类上面、方法上面、属性上面。

2.注解的开发准备

- 导入jar包（导入基本jar包、导入一个aop的jar包）
- 创建类，创建方法
- 创建spring配置文件，引入约束
 - 用配置方式管理bean，引入约束beans
 - 做spring的ioc注解开发，还要引入新的约束
- 配置开启注解扫描
示例：
```
 <?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"  
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
    xmlns:aop="http://www.springframework.org/schema/aop"  
    xmlns:context="http://www.springframework.org/schema/context"  
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd  
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd  
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd"> 
    <!--开启注解扫描，到包里面扫描类、方法、属性上面是否有注解-->
    <context:component-scan base-package="{写要创建的对象的类的包所在的位置，逗号分隔}"></context:component-scan> 
	<!--只会扫描属性上面的注解-->
	<context:annotation-config></context:annotation-config>
</beans>
```
3.注解创建对象
- 在要创建对象的类上面加上注解
@Component(value="xxx")  作用同于：<bean id="xxx" class="..."/>

- 创建对象有四个注解，spring提供三个Component的三个衍生注解（目前功能一致，是为了让标注本身的用途清晰）
 - Controller	web层
 - Service		业务层
 - Repository	持久层


- @Scope(value="")   //prototype表示多实例，不写表示单实例 


4.用注解方式注入属性

 - 在属性上使用注解完成注入
  - @Autowired 根据类自动装配
  - @Resource(name="")  根据value装配 


##配置文件和注解混合使用

1.创建对象操作使用配置文件方式
2.注入属性的操作使用注解方式

















