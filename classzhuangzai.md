---
title: JVM中的类装载器
date: 2018-06-04 01:16:35
tags:
---

#JVM类装载器

##class装载验证流程

 - 加载
  - 装载类的第一个阶段
  - 取得类的二进制流
  - 转为方法区数据结构
  - 在Java堆中生成对应的java.lang.Class对象
  <!--more-->
 - 链接
  - 验证
   - 目的：保证Class格式流是正确的
    - 文件格式的验证
     - 是否以0xCAFEBABE开头
     - 版本号是否合理
    - 元数据验证
     - 是否有父类
     - 是否继承了final类
     - 非抽象类实现了所有的抽象方法
    - 字节码验证（很复杂）
     - 运行检查
     - 栈数据类型和操作码数据参数温和
     - 跳转指令指定到合理的位置
    - 符号引用验证
     - 常量池中描述类是否存在
     - 访问的方法或者字段是否存在且有足够的权限
  - 准备
   - 分配内存，并为类设置初始值（方法区中）
    - public static int v=1
    - 在准备阶段中，v会被设置为0
    - 在初始化的<clinit>中才会被设置为1
    - 对于static final类型，在准备阶段就会被赋上正确的值
  - 解析
   - 符号引用替换为直接引用（指针或地址偏移量 引用对象一定在内存中）
 - 初始化
  - 执行类构造器<clinit>
   - static变量 赋值语句
   - static()语句
  - 子类的<clinit>调用前保证父类的<clinit>被调用
  - <clinit>是线程安全的


##类加载器ClassLoader
 - ClassLoader是一个抽象类
  - 重要方法：
   - public loadClass 载入并返回一个类
   - protected final Class 定义一个类 不公开调用
   - protected Class<?> findClass 	loadClass回调该方法，自定义ClassLoader的推荐做法
   - protected final Class<?> findLoadedClass 寻找已经加载的类  
 - ClassLoader的实例讲读入Java字节码将类装载到JVM中
 - ClassLoader可以定制，满足不同的字节码流获取方式
 - ClassLoader负责类装载过程中的加载阶段

##JDK中ClassLoader
 - Bootstrap ClassLoader  	rt.jar/-Xbootclasspath
 - Extension ClassLoader	%JAVA_HOME%/lib/ext/*.jar
 - App ClassLoader			Classpath下
 - Custom ClassLoader

 注：协同工作，自底向上检查类是否加载，自顶向下尝试加载类


##打破常规模式

 - Thread.setContextClassLoader()   //可以突破双亲模式的局限
  - 上下文加载器
  - 是一个角色
  - 用以解决顶层ClassLoader无法访问底层ClassLoader的类的问题
  - 基本思想是，在顶层的ClassLoader.传入底层ClassLoader的实例

 -双亲模式的破坏
  - 双亲模式是默认的模式，但是不是必须这么做
  - Tomcat的WebappClassLoader就会先加载自己的Class，找不到在委托parent
  - OSGI的ClassLoader形成网状结构，根据需要自由加载Class

##热替换

含义：当一个class被替换后，系统无需重启，替换的类立即生效