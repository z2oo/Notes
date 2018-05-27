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





