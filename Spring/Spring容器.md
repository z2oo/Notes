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

除了提供 BeanFactory 所支持的全部功能外，ApplicationContext还有如下额外功能
- ApplicationContext 默认会预初始化所有的 singleton Bean，也可通过配置取消预初始化功能
- ApplicationContext 继承 MessageSource 接口，因此提供国际化支持
- 资源访问，比如访问 URl 和文件
- 事件机制
- 同时加载多个配置文件
- 以声明式方式启动并创建 Spring 容器

ApplicationContext 包括 BeanFactory 的全部功能，因此建议优先使用 ApplicationContext，除非对于某些内存非常关键的应用，才考虑使用 BeanFactory  

当系统创建 ApplicationContext 容器时，默认会预初始化所有的 singleton Bean。也就是说，当 ApplicationContext 容器初始化完成后，容器会自动初始化所有的 singleton Bean，包括调用构造器创建该 Bean 的实例，并根据<property>元素执行 setter 方法。这意味着：
> 系统前期创建 ApplicationContext 时将有较大的系统开销，但一旦 ApplicationContext 初始化完成，程序后面获取 singleton Bean 实例时将拥有较好的性能  


---

Spring 的国际化支持，其实是建立在 Java 程序国际化的基础之上的。其核心思路都是将程序中需要实现的国际化的信息写入资源文件，而代码中仅仅使用相应的各信息的 Key  

### ApplicationContext的事件机制
ApplicationContext 的事件机制是观察者设计模式的实现，通过 ApplicationEvent 类和 ApplicationListener 接口，可以实现 ApplicationContext 的事件处理  

如果容器中有一个 ApplicationListener Bean，每当 ApplicationContext 发布 ApplicationEvent 时，ApplicationListener Bean 将自动触发  

Spring 的事件框架有如下两个重要成员
- ApplicationEvent：容器事件，必须由 ApplicationContext 发布
- ApplicationListener：监听器，可由容器中的任何监听器 Bean 担任

实际上，Spring 的事件机制与所有的事件机制都基本相似，它们都需要由事件源、事件和事件监听器组成；只是此处的事件源是 ApplicationContext，且事件必须由 Java程序显式触发  



```
sequenceDiagram
程序->>ApplicationContext: 发布ApplicationEvent事件
ApplicationContext->>ApplicationListener: 激发监听器

```
只要一个 Java 类继承了 ApplicationEvent 基类，那该对象就可作为 Spring 容器的容器事件  

容器事件的监听器类必须实现 ApplicationListener 接口，实现该接口必须实现如下方法：
- onApplicationEvent(ApplicationEvent event)：每当容器内发生任何事件时，此方法都被触发

当系统创建 Spring 容器、加载 Spring 容器时会自动触发容器事件，容器事件监听器可以监听到这些事件。除此之外，程序也可调用 ApplicationContext 的 pulishEvent() 方法来主动触发容器事件

---

前面都是程序先创建 Spring 容器，再调用 Spring 容器的 getBean() 方法来获取 Spring 容器中的 Bean。在这种访问模式下，程序总是持有 Spring 容器的引用  

但在 Web 应用中，Spring 容器通常采用 声明式 方式配置产生：  

开发者只要在 web.xml 文件中配置一个 Listener，该 Listener 将会负责初始化 Spring 容器，前端 MVC 框架可以直接调用 Spring 容器中的 Bean，无须访问 Spring 容器本身。在这种情况下，容器中的 Bean 处于容器管理下，无须主动访问容器，只需接受容器的依赖注入即可  

在某些情况下，Bean 需要实现某个功能(比如该 Bean 需要输出国际化消息，或者该 Bean 需要向 Spring 容器发布事件...)，但该功能必须借助于 Spring 容器才能实现，此时就必须让该 Bean 先获取 Spring 容器，然后借助于 Spring 容器来实现该功能  

为了让 Bean 获取它所在的 Spring 容器，可以让该 Bean 实现 BeanFactoryAware 接口

