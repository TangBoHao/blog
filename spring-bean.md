---
title: spring中的bean
date: 2018-05-09 18:07:11
---
##Bean的配置项：

 - Id 唯一标识
 - Class 要实例化的类
 - Scope 范围 作用域
 - Constructor arguments 构造器参数
 - Properties 属性
 - Autowiring mode 自动装配的模式
 - lazy-initialization mode 懒加载模式
 - Initialization/destruction method 初始化和销毁的方法

<!--more-->



##Bean的作用域
 - singleton：单例。每一个Bean容器中只存在一份
 - prototype：每次使用创建新的实例，destroy方法不生效。
 - request： 每次http请求创建一个实例且仅在当前request内有效。
 - session：同时，每次http请求创建。当前session有效。
 - global session：基于porlet的web有效（porlet定义了global session），如果在web中，同session。







##Bean的生命周期


###初始化：
 - 实现org.springframework.beans.factory.InitializingBean接口，覆盖afterPropertiesSet方法
	
 - 配置init-method
```xml
	<bean id="exampleInitBean" class="examples.ExampleBean" init-method="init" />
```
###销毁:
 - 实现org.springframework.beans.factory.DisposableBean接口，覆盖destory方法

 - 配置init-method
```	xml
	<bean id="exampleInitBean" class="examples.ExampleBean" destroy-method="cleanup" />
```
 - 以上都是针对某个Bean的初始化和销毁
配置全局默认初始化、销毁方法：
```xml
<beans default-init-method="init" default-destory-method="destroy"></beans>
```

同时定义的时候，方法的执行顺序：实现接口 先于 配置方式（全局默认的方法不生效）



###资源接口
Spring中提供了一些以Aware结尾的接口，实现了Aware接口的bean在初始化之后，可以获得相应资源。
通过Aware接口，可以对Spring相应资源进行操作。
为对Spring进行简单的扩展提供了方便的入口。
例：
 - ApplicationContextAware：
 - BeanNameAware:


###Bean的自动装配:
 - NO ：不做任何操作
 - byname：根据属性名进行自动装配。此选项检查容器并根据名字查找 与属性完全一致的bean，并将其与属性自动装配。
 - byType：如果容器中存在一个与指定属性类型相同的bean，那么将与该属性自动装配。如果存在多个该类型的bean，那么抛出异常，并指出不能使用byType方式进行自动装配。如果没有找到相匹配的bean，则什么也不发生。
 -  Construtor：与byType类似，不同在于他应用于构造器参数。如果容器中没有找到与构造器参数类型一致的bean，那么抛出异常。

###Resources&ResourceLoader
 - Resources:针对资源文件的统一接口
 - UrlResource：url对应的资源，根据一个URL地址即可构建。
 -  ClassPathResource：获取类路径下的资源文件。
 - FileSystemResource：获取文件系统里面的资源。
 - ServletContextResource:ServletContext封装的资源，用于访问ServletContext环境下的资源。
 -  InputStreamResource:针对输入流封装的资源。
 - ByteArrayResource:针对字节数组封装的资源。

###ResourceLoader:
接口定义：
```java
public interface ResourceLoader{
	Resource getResource(String location);
}
```

参数前缀：
 - classpath：从classpath加载
 - file：从文件系统加载
 - http：从url加载
 - （none）：依赖于ApplicationContext
