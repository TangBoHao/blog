---
title: hibernate的入门概念
date: 2018-06-04 00:25:05
tags:
---
#hibernate入门概念

###javaEE三层：
1、web层
2、service层
3、dao层（对数据库进行crud操作）


hibernate：持久层框架、orm框架

orm：对象关系映射
1.
让实体类和数据库表进行一一对应
让实体类属性和表里面的字段对应
2.不需要直接操作数据库，直接操作实体类对象
<!--more-->

###建立映射方法
1.hibernate要求实体类有一个属性是唯一的
2.使用hibernate的时候，可以不用手动创建表，hibernate会自动创建表。
3.配置实体类和数据库表的映射关系。使用配置文件实现。
 - 创建xml格式的配置文件
  映射配置文件名称和位置没有固定要求，建议：在实体类所在的包里面创建，名称-实体类名称.hbm.xml
 - 配置是xml戈斯和，在配置文件中首先要引入xml约束。在里面引入dtd约束。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC 
    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
```
 - 配置映射关系
 1. 配置类和表对应
 class标签
 name属性：实体类全路径
 table属性：数据库表名称
 2. 配置实体类id和表id对应
 id标签
 name属性：实体类里面id属性名称
 column属性：表字段名称（主键）
 在标签内设置数据库id增长策略 generator class="native" 生成表的id值就是主键自动增长
 3. 配置其他属性和表字段对应
 property标签
 示例：
```xml
<hibernate-mapping>
	<class name="com.User" table="t_User">
	<id name="uid" column="uid">
		<generator class="native"></generator>
	</id>
	<property name="username" column="username"></property>
	<property name="password" column="password"></property>
	<property name="address" column="address"></property>
	</class>
</hibernate-mapping>
```

4. 创建hibernate的核心配置文件
注：
 1. 核心配置文件的名称和位置都是固定的。必须在src下面，名称必须为hibernate.cfg.xml
 2. 引入dtd约束
 3. hibernate操作过程中，只会加载核心配置文件，其他配置文件不会被加载。

 过程：
 1. 配置数据库信息
  查找hibernate.properties.template文件
  connection.driver_class 驱动名
  connection.url 数据库路径
  connection.username 用户名
  connection.password 密码
  show_sql 输出sql语句
  format_sql 对sql语句进行格式化

 2. 配置hibernate信息
  show_sql 输出sql语句
  format_sql 对sql语句进行格式化
  hbm2ddl.atuo 值update：如果已经有了这个表 则进行更新
  dialect 配置数据库的方言 让hibernate识别不同数据库的关键字

 3. 把映射文件放到核心配置文件中
  mapping标签 属性resource 以src为根目录

  示例：
```xml
<?xml version="1.0" encoding="utf-8"?>  
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"  
"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd"> 
  <hibernate-configuration>
  <session-factory>
    <property name="hibernate.show_sql">true</property>  
        <property name="hibernate.format_sql">true</property>  
        <property name="hibernate.dialect">dialect.SQLiteDialect</property>  
        <property name="hibernate.connection.driver_class">org.sqlite.JDBC</property>  
        <property name="hibernate.connection.url">jdbc:sqlite:Usertest.db</property>  
        <property name="hibernate.connection.username"></property>  
        <property name="hibernate.connection.password"></property>  
       
        <mapping resource="com/User.hbm.xml"/>  
  </session-factory>
   </hibernate-configuration>  
    

```

mysql数据库配置文件示例：
```xml
<?xml version="1.0" encoding="utf-8"?>  
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"  
"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd"> 
<hibernate-configuration>
  <session-factory>
  <property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
<property name="hibernate.connection.url">jdbc:mysql://localhost:3306/hibernatetest</property>
<property name="hibernate.connection.username">root</property>
<property name="connection.password">123456</property>
<property name="hibernate.dialect">org.hibernate.dialect.MySQL5InnoDBDialect</property>  
<property name="hibernate.show_sql">true</property>
<property name="hibernate.format_sql">true</property>
<property name="hibernate.hbm2ddl.auto">update</property>
<mapping resource="com/User.hbm.xml"/>
  </session-factory>
</hibernate-configuration>
```
注：

 - hibernate.hbm2ddl.auto:
自动创建|更新|验证数据库表结构。如果不是此方面的需求建议set value="none"
常用属性有create、update
 - create：
   每次加载hibernate时都会删除上一次的生成的表，然后根据你的model类再重新来生成新表，哪怕两次没有任何改变也要这样执行，这就是导致数据库表数据丢失的一个重要原因。
 - create-drop ：
    每次加载hibernate时根据model类生成表，但是sessionFactory一关闭,表就自动删除。
 - update：
    最常用的属性，第一次加载hibernate时根据model类会自动建立起表的结构（前提是先建立好数据库），以后加载hibernate时根据model类自动更新表结构，即使表结构改变了但表中的行仍然存在不会删除以前的行。要注意的是当部署到服务器后，表结构是不会被马上建立起来的，是要等应用第一次运行起来后才会。
validate ：
    每次加载hibernate时，验证创建数据库表结构，只会和数据库中的表进行比较，不会创建新表，但是会插入新值。


 ###代码实现添加操作
 1. 加载hibernate核心配置文件
 2. 创建SessionFactory对象
 3. 使用SessionFactory创建session对象
 4. 开启事务
 5. 写具体逻辑crud操作
 6. 提交事务
 7. 关闭资源
 
 代码示例：
```java
    //加载hibernate核心配置文件
    //会到src下面找hibernate.cfg.xml文件
    //在hibernnate里面封装对象
    Configuration cfg=new Configuration();
    cfg.configure();
    //创建SessionFactory对象
    //读取核心配置文件，创建SessionFactory
    //在这个过程中，根据映射关系，在数据库中创建表
    SessionFactory sf=cfg.buildSessionFactory();
    //创建session对象
    Session session=sf.openSession();
    //开启事务
    Transaction tx=session.beginTransaction();
    //进行crud操作
    User user=new User();
    user.setUsername("小陈");
    user.setPassword("123");
    user.setAddress("武汉");
    //调用session里面的方法添加
    session.save(user);

    //提交事务
    tx.commit();
    //关闭资源
    session.close();
    sf.close();
```

注：要解决中文乱码的问题，可以在定义的数据库的url后面添加?useUnicode=true&amp;characterEncoding=utf-8。
其中&amp;为&的转义字符。xml中特殊符号要用转义字符。












