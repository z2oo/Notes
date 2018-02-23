## Java配置管理
通常采用的三个 Annotation  
- @Configuration：用于修饰一个 Java 配置类
- @Bean：用于修饰一个方法，将该方法的返回值定义成容器中的一个 Bean
- @Value：用于修饰一个 Field，用于为该 Field 配置一个值，相当于配置一个变量

一旦使用了 Java 配置类 来管理 Spring 容器中的 Bean 及其依赖关系，此时就需要使用如下方式来创建 Spring 容器：
```
//创建 Spring 容器
ApplicationContext ctx=new AnnotationConfigApplicationContext(AppConfig.class);
```
上面的 AnnotationConfigApplicationContext 类会根据 Java 配置类来创建 Spring 容器。不仅如此，该类还提供了一个 **register**(Class) 方法用于添加 Java 配置类  

获得 Spring 容器之后，接下来利用 Spring 容器获取 Bean 实例、调用 Bean 方法就没有任何特别之处了  

使用 Java 配置类时，还有如下常用的 Annotation：
- @Import：修饰一个 Java 配置类，用于向当前 Java 配置类中导入其他 Java 配置类  
- @Scope：用于修饰一个方法，指定该方法对应的 Bean 的生命域
- @Lazy：用于修饰一个方法，指定该方法对应的 Bean 是否需要延迟初始化
- @DependsOn：用于修饰一个方法，指定在初始化该方法对应的 Bean 之前初始化指定的 Bean


① 如果以 XML 配置为主，就需要让 XML 配置能加载 Java 类配置：
```
<beans>
    <!-- 加载 Java 配置类-->
    <bean class="org.crazyit.app.config.AppConfig"/>
</beans>
```
由于以 XML 配置为主，因此应用创建 Spring 容器时，还是以这份 XML 配置文件为参数来创建 ApplicationContext 对象。那么 Spring 会加载这份 XML 配置文件，再根据这份 XML 配置文件的指示去加载指定的 Java 配置类  

② 如果以 Java 类配置为主，就需要让 Java 配置类能加载 XML 配置。这就需要借助于 @ImportResource 注解，这个注解可修饰 Java 配置类，用于导入指定的 XML 配置文件：
```
@Configuration
//导入 XML 配置
@ImportResource("classpath:/beans.xml")
public class MyConfig{
    ...
}
```
由于以 Java 类配置为主，因此应用创建 Spring 容器时，应以 Java 配置类为参数，通过创建 AnnotationConfigApplicationContext 对象作为 Spring 容器。那么 Spring 会先加载这个 Java 配置类，再根据这个 Java 配置类的指示去加载指定的 XML 配置文件  

