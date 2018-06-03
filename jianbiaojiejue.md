---
title: hibernate不能自动建表的解决方法
date: 2018-06-04 00:44:53
tags:
---
##hibernate不能自动建表的解决办法

hibernater不能自动建表的原因有很多会报数据表不存在的错误。在网上查了一下，各种说法都有。总结一下，大概有以下几种原因：

1.
####数据库的方言和数据库的版本不匹配
MySQL数据库的方言有以下几种方言：
<!--more-->
兼容性模式
```
<property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
```
需要注意的是5.5一下版本可以使用
```
<property name="hibernate.dialect">org.hibernate.dialect.MySQLInnoDBDialect</property>
```
Mysql数据库版本是5.5的，设置事务性方言时要修改，就是加一个5
```
<property name="dialect">org.hibernate.dialect.MySQL5InnoDBDialect</property>
```

2.
####表的字段设置有问题
如果数据表的字段不符合规范，比如使用了关键字、重复定义等，就会导致无法创建数据表。

3.
####主键的类型或者生成方式配置有问题
主键的类型，实体类中对应主键的属性，都应该是int型。在generator标签中的class属性，也要注意。
我设置成native不能自动建表，设置成increment就可以了。

生成主键的参数有以下几种：
 - assigned
　　主键由外部程序负责生成，无需Hibernate参与。

 - hilo
　　通过hi/lo 算法实现的主键生成机制，需要额外的数据库表保存主键生成历史状态。

 - seqhilo
　　与hilo 类似，通过hi/lo 算法实现的主键生成机制，只是主键历史状态保存在Sequence中，适用于支持Sequence的数据库，如Oracle。

 - increment
　　主键按数值顺序递增。此方式的实现机制为在当前应用实例中维持一个变量，以保存着当前的最大值，之后每次需要生成主键的时候将此值加1作为主键。这种方式可能产生的问题是：如果当前有多个实例访问同一个数据库，那么由于各个实例各自维护主键状态，不同实例可能生成同样的主键，从而造成主键重复异常。因此，如果同一数据库有多个实例访问，此方式必须避免使用。

 - identity
　　采用数据库提供的主键生成机制。如DB2、SQL Server、MySQL中的主键生成机制。

 - sequence
　　采用数据库提供的sequence 机制生成主键。如Oralce 中的Sequence。

 - native

　　由Hibernate根据底层数据库自行判断采用identity、hilo、sequence其中一种作为主键生成方式。

 - uuid.hex
　　由Hibernate基于128 位唯一值产生算法生成16 进制数值（编码后以长度32 的字符串表示）作为主键。

 - uuid.string
　　与uuid.hex 类似，只是生成的主键未进行编码（长度16）。在某些数据库中可能出现问题（如PostgreSQL）。

 - foreign
　　使用外部表的字段作为主键。一般而言，利用uuid.hex方式生成主键将提供最好的性能和数据库平台适应性。