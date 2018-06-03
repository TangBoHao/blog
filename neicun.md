---
title: JVM内存模型
date: 2018-06-04 01:05:57
tags:
---
#JVM内存模型

##JVM栈帧：
内容：

 - 局部变量表
 - 操作数栈
 - 动态链接
 - 方法返回地址
<!--more-->

使用最广的java虚拟机为HotSpot,JRockit ,在JDK8进行了整合。
在HotSpot的基础上整合JRockit的好的特性。
可以用jvm运行的语言：Groovy，Clojure，Scala等。


##java语言规范
语法
变量
类型
文法

##JVM规范
class文件类型
运行时数据
栈帧
虚拟机的启动
虚拟机的指令

JVM指令集：

 - 类型转化
 l2i

 - 出栈入栈操作
 aload astore

 - 运算
 iadd isub

 - 流程控制
 ifeq ifne

 - 函数调用
 invokevirtual invokeinterface invokespecial invokestatic 

##JVM需要对java Library提供以下支持
 - 反射 java.lang.reflect
 - ClassLoader
 - 初始化class和interface
 - 安全相关 java.security
 - 多线程
 - 弱引用

##JVM的编译
 - 源码到JVM指令的对应格式
 - Javap
 - 反汇编的格式
 索引 操作码 操作数 注解

##JVM的启动流程
1. 装载配置 //根据当前路径和系统版本号寻找jvm.cfg
2. 根据配置寻找JVM.dll  //JVM.dll为JVM的主要实现
3. 初始化JVM获得JNIEnv接口  	(JNIEnv为JVM接口，findClass等操作通过它来实现)
4. 找到main方法并运行

##PC寄存器
 - 每一个线程有一个PC寄存器
 - 在线程创建时创建
 - 指向下一个指令的地址
 - 执行本地方法时，PC的值为undifined

##方法区
 - 保存装载的类信息
 类型的常量池 
 字段 方法信息
 方法字节码
 - 通常和永久区关联在一起
//永久区：保存一些相对稳定的数据

##Java堆
 - 和程序开发密切相关
 - 应用系统对象都保存在Java堆中
 - 所有线程共享Java堆
 - 对分代GC来说，堆也是分代的
 - GC的主要工作区间   eden s0 s1 tenured

##Java栈
 - 线程私有
 - 栈由一系列帧组成
 - 帧保存每一个方法的局部变量表（也包含参数）（每一个槽位有32位），操作数栈（Java没有寄存器，所有参数传递通过操作数栈），常量池指针
 - 每一次方法调用创建一个帧，并压栈

####栈上分配内存
 - 小对象，没有逃逸（不在其他地方使用）的情况下，可以直接分配在栈上
 - 分配在栈上，可以自动回收，减轻GC压力
 - 大对象或者逃逸对象无法在栈上分配


##JVM内存模型
 - 每一个线程有一个工作内存和主存独立
 - 工作内存存放贮存中变量的值的拷贝

 注：对于普通变量，一个线程中更新的值，不能马上反应在其他线程中。如果需要在其他线程中立即可见，需要使用volatile关键字。

可见性：一个线程修改了变量，其他的线程可以立即知道

####保证可见性的方法：
 - volatile
 - synchronized（unlock之前，写变量值回主存）
 - final（一旦初始化完成，其他的线程都可见）



#常用JVM配置参数

##Trace跟踪参数
打印GC的信息、监控类的加载

##堆的分配参数
-Xmx -Xms 指定最大堆和最小堆
-Xmn 设置新生代大小
-XX:NewRatio 设置老年代和新生代的比值
-XX:SurvivorRatio  设置两个Survivor区和eden的比
-XX:Permsize 设置永久区的大小


##栈的分配参数
-Xss
 - 通常只有几百k
 - 决定了函数调用的深度
 - 每个线程都有独立的栈空间
 - 局部变量、参数，分配在栈上


