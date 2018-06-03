## SpringMVC 工作原理
首先先上一张 SpringMVC 的工作原理图：
  
![](https://images2015.cnblogs.com/blog/249993/201612/249993-20161212142542042-2117679195.jpg)  

我们可以知道 SpringMVC 的前置控制器的核心是 `DispatcherServlet`   

1. 它接受浏览器的请求之后将会调用 `HandlerMapping` (处理映射器)  

2. `HandlerMapping` 找到具体的处理器(可以通过 XML 或者注解)，生成处理器对象及处理器拦截器(如果有) ，并返回给 `DispatcherServlet`  

3. `DispatcherServlet` 调用 `HandlerAdapter` (处理器适配器) 

4. `HandlerAdapter` 经过适配调用具体的处理器(controller，也叫后置控制器) 

5. `Controller` 执行完成后返回 ModelAndView  

6. `HandlerAdapter` 将 `controller` 执行的结果 `ModelAndView` 返回给 `DispatcherServlet` 

7. `DispatcherServlet` 将 `ModelAndView` 传给 `ViewResolver`(视图解析器) 

8. `ViewResolver` 解析后返回具体的 `View`

9. `DispatcherServlet` 根据 `View` 进行渲染视图 (即将模型数据填充至视图中)   


## <mvc:annotation-driven/>
当我们通过在 `springmvc-servlet.xml` 中加上
`<mvc:annotation-driven/>`，它会自动注册 `DefaultAnnotationHandlerMapping` 与 `AnnotationMethodHandlerAdapter` 这两个 bean。  

也就是我们可以不用再配置 `HandlerMapping` 和 `HandlerAdapter` 了，但是这样使用默认的配置的话，就不能指定拦截器了  

## <Context:annotation-config/>
在基于注解方式配置 Spring 的配置文件中，通常会见到  
```
<context:annotation-config/>
```
这样一条配置，它的作用是隐式地向 Spring 容器注册  
```
AutowiredAnnotationBeanPostProcessor
CommonAnnotationBeanPostProcessor
PersistenceAnnotationBeanPostProcessor
RequiredAnnotationBeanPostProcessor
```
这4个 `BeanPostProcessor`  

注册这4个 `BeanPostProcessor` 的作用，就是为了让你的系统能够识别相应的注解  


其中，如果你想使用 `@Autowired` 注解，那么就必须事先在 Spring 容器中声明 `AutowiredAnnotationBeanPostProcessor` Bean  

如果你想使用 `@Resource`、`@PostConstruct`、`@PreDestroy` 等注解，就必须声明 `CommonAnnotationBeanPostProcessor`   

如果你想使用 `@PersistenceContext` 注解，就必须声明 `PersistenceAnnotationBeanPostProcessor`   

如果你想使用 `@Required` 的注解，就必须声明 `RequiredAnnotationBeanPostProcessor`  

一般来说，这些注解我们还是比较常用，尤其是 `Autowired` 的注解，在自动注入的时候经常使用，所以如果总是按照传统的方式一条一条配置显得有些繁琐和没有必要，于是 `Spring` 给我们提供 `<context:annotation-config/>` 的简化配置方式，自动帮你完成声明  

不过呢，我们使用注解一般都会配置扫描包路径选项  
```
<context:component-scan base-package="xx.xx"/>
```
该配置项其实也包含了自动注入上述 `processor` 的功能，因此当使用了 `<context:component-scan/>`后，就可以将 `<context:annotaion-confg/>` 移除掉  

## 组件启动顺序
在 Web 容器中，有监听器( Listener )、过滤器( Filter )和 Servlet  

它们的启动顺序是：  
监听器 > 过滤器 > 拦截器 > Servlet  

## 监听器
### 监听器的划分
#### 按监听的对象划分
1. 用于监听应用程序环境对象 (ServletContext) 的事件监听器  
2. 用于监听用户会话对象 (HttpSession) 的事件监听器
3. 用于监听请求消息对象 (ServletRequest) 的事件监听器  

#### 按监听的事件划分
1. 监听域对象自身的创建和销毁的事件监听器  
2. 监听域对象中的属性的增加和删除的事件监听器
3. 监听绑定到 HttpSession 域中的某个对象的状态的事件监听器

监听域对象 及 创建和销毁监听器  
ServletContext —— ServletContextListener  
HttpSession —— HttpSessionListener  
ServletRequest —— ServletRequestListener  


ServletContextListener 主要用途
1. 定时器
2. 全局属性对象
  

HttpSessionListener 主要用途
1. 统计在线人数
2. 记录访问日志
  

ServletRequestListener 主要用途
1. 读取参数
2. 记录访问历史
  

监听域对象  属性的增加和删除监听器  
ServletContext —— ServletContextAttributeListener  
HttpSession —— HttpSessionAttributeListener  
ServletRequest —— ServletRequestAttributeListener 

当某个类实现了 HttpSessionBindingListener 接口，则只要在 Sesion 域中，当这个对象绑定或解除绑定则会调用相应的方法  

  
  
## SpringMVC 的拦截器和过滤器
### 过滤器：
依赖于 Servlet 容器，在实现上基于函数回调，可以对几乎所有请求进行过滤，但是缺点是一个过滤器实例只能在容器初始化时调用一次。   

使用过滤器的目的是用来做一些过滤操作，获取我们想要获取的数据，比如：在过滤器中修改字符编码；在过滤器中修改 HttpServletRequest 的一些参数，包括：过滤低俗文字、危险字符等  

### 拦截器：
依赖于 Web 框架，在 SpringMVC 中就是依赖于 SpringMVC 框架。在实现上基于 Java 的反射机制，属于面向切面编程 (AOP) 的一种运用。  

由于拦截器是基于 Web 框架的调用，因此可以使用 Spring 的依赖注入进行一些业务操作，同时一个拦截器实例在一个 controller 生命周期之内可以多次调用。  

但是缺点是只能对 controller 请求进行拦截，对其他的一些比如直接访问静态资源的请求则没办法进行拦截处理  

---

对于整个 SpringMVC 的执行流程来说，如果加上了拦截器和过滤器，其最终的执行流程如下图：  
![](http://img4.07net01.com/upload/images/2016/12/07/100928071536053.png)  
