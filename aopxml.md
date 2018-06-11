---
title: pring的aop操作-xml配置实现
date: 2018-06-11 00:55:35
tags:
---
##spring的aop操作-xml配置实现

1.在spring里面进行aop操作，使用aspectj实现。

 - aspectj不是spring的一部分，是一个单独的框架，和spring一起使用进行aop操作
 - spring2.0以后新增了对aspectj的支持。

2.使用aspectj实现aop有两种方式：xml配置和注解。
<!--more-->
##AOP操作准备
1.除了导入基本的jar，还要导入aop相关的jar包。
2.创建spring核心配置文件。导入aop的约束。

###使用表达式配置切入点
常用的表达式：

- execution(<访问修饰符>?<返回类型><方法名>(<参数>)<异常>)
 - exection(*aoptest.Book.add(..))
 - exection(*aoptest.Book.*(..))
 - exection(* *.*(..))
 - exection(*save*(..))

配置文件具体实现：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"        
    xmlns:mvc="http://www.springframework.org/schema/mvc"     
    xmlns:tx="http://www.springframework.org/schema/tx"  
    xmlns:aop="http://www.springframework.org/schema/aop"  
    xmlns:context="http://www.springframework.org/schema/context"  
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"              
    xsi:schemaLocation="                                               
            http://www.springframework.org/schema/beans    
            http://www.springframework.org/schema/beans/spring-beans.xsd    
            http://www.springframework.org/schema/context     
            http://www.springframework.org/schema/context/spring-context.xsd    
            http://www.springframework.org/schema/mvc    
            http://www.springframework.org/schema/mvc/spring-mvc.xsd  
            http://www.springframework.org/schema/tx   
            http://www.springframework.org/schema/tx/spring-tx.xsd  
            http://www.springframework.org/schema/aop  
            http://www.springframework.org/schema/aop/spring-aop.xsd "  >  
       <!--配置对象-->
       <bean id="book" class="aoptest.Book"></bean>
       <bean id="mybook" class="aoptest.MyBook"></bean>
       <aop:config>
       <!--配置切入点-->
       <aop:pointcut expression="execution(* aoptest.Book.add(..))" id="point1"/>
       <!--配置切面-->
       <aop:aspect ref="mybook">
       <!--配置增强类型 method：增强类中哪个方法作为前置-->
       <aop:before method="before1" pointcut-ref="point1"/>
       <!--配置后置通知-->
       <aop:after-returning method="after1" pointcut-ref="point1"/>
       <!--配置环绕通知-->
       <aop:around method="around1" pointcut-ref="point1"/>
       </aop:aspect>
       </aop:config>
            
</beans>
```

增强类的具体实现：
```java
package aoptest;

import org.aspectj.lang.ProceedingJoinPoint;

public class MyBook {

  public void  before1()
  {
    System.out.println("前置增强");
  }
  
  public  void after1()
  {
    System.out.println("后置通知");
  }
  
  public void around1(ProceedingJoinPoint point) throws Throwable
  {
    System.out.println("环绕之前");
    point.proceed(); //调用切入点的方法
    System.out.println("环绕之后");
  }
}

```

调用的具体代码：
```java
package aoptest;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class aoptest {

  public static void main(String[] args)
  {
    //加载配置文件的方法和使用bean一样
    ApplicationContext context=new ClassPathXmlApplicationContext("aopbean.xml");
    Book book=(Book)context.getBean("book");
    book.add();
  }
}

```

增强被执行的先后顺序：
```
前置增强
环绕之前
add......
环绕之后
后置通知

```