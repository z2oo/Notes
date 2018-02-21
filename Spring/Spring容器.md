## 使用Spring容器
Spring有两个核心接口：**BeanFactory** 和 **ApplicationContext**，其中 ApplicationContext 是 BeanFactory 的子接口。  

它们都可代表 Spring 容器，Spring 容器是生成 Bean 实例的工厂，并管理容器中的 Bean  

在基于 Spring 的 JavaEE 应用中，所有的组件都被当成 Bean 处理，包括数据源、Hibernate 的 SessionFactory、事务管理器等  

应用中的所有组件都处于 Spring 的管理下，都被 Spring 以 Bean 的方式管理，Spring 负责创建 Bean 实例，并管理其生命周期  

Spring 里的 Bean 是非常广义的概念，任何的 Java 对象、Java 组件都被当成 Bean 处理。对于 Spring 而言，一切 Java 对象都是 Bean  

Bean 在 Spring 容器中运行，无须感受 Spring 容器的存在，一样可以接受 Spring 的依赖注入，包括 Bean 成员变量的注入、协作者的注入、依赖关系的注入等  

Java 程序面向接口编程，无须关心 Bean 实例的实现类；但 Spring 容器负责创建 Bean 实例，因此必须精确知道每个 Bean 实例的实现类，故 Spring 配置文件必须指定 Bean 实例的实现类  

### Spring容器
Spring 容器最基本的接口就是 BeanFactory。BeanFactory负责配置、创建、管理Bean，它有一个子接口：ApplicationContext，因此也被称为 Spring上下文。Spring 容器还负责管理 Bean 与 Bean 之间的依赖关系  

调用者只需使用 getBean() 方法即可获得指定 Bean 的引用，无须关心 Bean 的实例化过程。Bean 实例的创建、初始化以及依赖关系的注入都是由 Spring 容器完成  

BeanFactory 常用的实现类是 DefaultListableBeanFactory  

ApplicationContext 是 BeanFactory 的子接口，对于大部分 JavaEE 应用而言，使用它作为 Spring 容器更方便。  
其常用实现类是：
- FileSystemXmlApplicationContext
- ClassPathXmlApplicationContext
- AnnotationConfigApplicationContext

如果在 Web 引用中使用 Spring 容器，则通常有 XMLWebApplicationContext、AnnotationConfigWebApplicationContext两个实现类  

创建 Spring 容器的实例时，必须提供 Spring 容器管理的 Bean 的详细配置信息。Spring 的配置信息通常采用 XML 配置文件来设置，因此，创建 BeanFactory 实例时，应该提供 XML 配置文件作为参数。  

XML 配置文件通常使用 Resource 对象传入  

> Resource 接口是 Spring 提供的资源访问接口，通过使用该接口，Spring 能以简单、透明的方式访问磁盘、类路径已经网络上的资源

大部分 Java EE 应用，可在启动 Web 应用时自动加载 ApplicationContext 实例，接受 Spring 管理的 Bean 无须知道 ApplicationContext 的存在，一样可以利用 ApplicationContext 的管理  

对于独立的应用程序，可以通过如下方法来实例化 BeanFactory  
```
//搜索类加载路径下的 beans.xml 文件创建 Resource 对象
Resource isr=new ClassPathResource("beans.xml");
//创建默认的 BeanFactory 容器
DefaultListableBeanFactory beanFactory=new DefaultListableBeanFactory();
//让默认的 BeanFactory 容器加载 isr 对应的 XMl 配置文件
new XmlBeanDefinitionReader(beanFactory).loadBeanDefinitions(isr);
```
或者采用如下代码来创建 BeanFactory
```
//搜索文件系统的当前路径下的 beans.xml 文件创建 Resource 对象
Resource isr=new FileSystemResource("beans.xml");
//创建默认的 BeanFactory 容器
DefaultListableBeanFactory beanFactory=new DefaultListableBeanFactory();
//让默认的BeanFactory 容器加载 isr 对应的 Xml 配置文件
new XmlBeanDefinitionReader(beanFactory).loadBeanDefinitions(isr);
```
如果应用需要加载多个配置文件来创建 Spring 容器，则应该采用 BeanFactory 的子接口 ApplicationContext 来创建 BeanFactory 的实例  

ApplicationContext 接口包含 FileSystemXMLApplicationContext 和 ClassPathXmlApplicationContext 两个常用的实现类  

如果需要同时加载多个 XML 配置文件来创建 Spring 容器，则可以采用如下方式：
```
//以类加载路径下的 beans.xml、service.xml 文件创建 ApplicationContext
ApplicationContext appContext=new ClassPathXmlApplicationContext("beans.xml","service.sml");
```
由于 ApplicationContext 本身就是 BeanFactory 的子接口，因此 ApplicationContext 完全可以作为 Spring 容器来使用，而且功能更强。当然，如果有需要，也可以把 ApplicationContext 实例赋给 BeanFactory 变量  


### 使用ApplicationContext
大部分时候，都不会使用 BeanFactory 实例作为 Spring 容器，而是使用 ApplicationContext 实例作为容器，因此也把 Spring 容器称为 Spring 上下文。ApplicationContext 是 BeanFactory 接口的子接口，它增强了 BeanFactory 的功能  

ApplicationContext 允许以声明式方式操作容器，无须手动创建它。可利用如 ContextLoader 的支持类，在 Web 应用启动时自动创建 ApplicationContext，当然，也可以采用编程方式创建 ApplicationContext  

