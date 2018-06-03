---
title: hibernate配置文件
date: 2018-06-04 00:35:38
tags:
---
##hibernate映射配置文件

1. 文件名和位置不固定
2. 标签name属性写实体类相关内容
 - class标签name属性是实体类的全路径
 - id标签和property标签的name属性值是实体类属性名称
3. id标签和property标签，column属性可以省略。
 - 默认值和name属性值一样
4. property标签type属性，设置生成表的字段的类型
<!--more-->

##hibernate核心配置文件

1. ```
<hibernate-configuration>
	<session-factory>
		配置内容写在这里
	</session-factory>
</hibernate-configuration>
```


2. 数据库部分必须，hibernate部分可选，映射文件必须

3. 核心配置文件名称和位置是固定的。src下面，名称为hibernate.cfg.xml.


