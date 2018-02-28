# Spring的“零配置”支持
## 搜索Bean类
Spring 没有采用 “约定优于配置” 的策略，Spring 依然要求程序员显式指定搜索哪些路径下的 Java 类，Spring 将会把合适的 Java 类全部注册成 Spring Bean。  

这需要使用 Annotation，即 注解  

Spring 提供如下几个 Annotation 来标注 Spring Bean  
- @Component：标注一个普通的 Spring Bean 类
- @Controller：标注一个控制器组件类
- @Service：标注一个业务逻辑组件类
- @Repository：标注一个 DAO 组件类

如果需要定义一个普通的 Spring Bean，则直接使用 @Component 标注即可，但如果用 @Repository、@Service 或 @Controller 来标注这些 Bean 类，这些 Bean 类将被作为特殊的 Java EE 组件对待，能更好地被工具处理，或与切面进行关联  

指定某些类可作为 Spring Bean 类使用后，最后还需要让 Spring 搜索指定路径，此时需要在 Spring 配置文件中导入 context Schema，并指定一个简单的 搜索路径  
```
<beans xmlns:context="http://www.springframework.org/schema/context"
...
>
    <!-- 自动扫描指定包及其子包下的所有 Bean 类-->
    <context:component-scan
        base-package="org.crazyit.app.service"/>
</beans>
```

上面配置文件中，指定 Spring 将会把 org.crazyit.app.service 包及其子包下的所有 Java 类都当成 Spring Bean 来处理，并为每个 Java 类创建对应的 Bean 实例  

在默认情况下，Spring 会自动搜索所有以 @Component、@Controller、@Service 和 @Repository 标注的 Java类，并将它们当成 Spring Bean 来处理  

还可以通过为 <component-scan> 元素添加 <include-filter>或 <exclude-filter> 子元素来指定 Spring Bean 类，只要位于指定路径下的 Java 类满足这种规则，即使这些 Java 类没有使用任何 Annotation 标注，Spring 一样会将它们当成 Bean 类来处理  

---

## 指定 Bean 的作用域
当使用 XML 配置方式来配置 Bean 实例时，可以通过 scope 属性来指定 Bean 实例的作用域，没有指定 scope 属性的 Bean 实例的作用域默认是 singleton  

当采用 零配置 方式来管理 Bean 实例来时，可以使用 @Scope Annotation，只要在该 Annotation 中提供作用域的名称即可，如：
```
//指定该 Bean 实例的作用域为 prototype 
@Scope("prototype")
//指定该类作为 Spring Bean，Bean 实例名为 axe
@Component("axe")
public class SteelAxe implements Axe{
    ...
}
```

---

## 使用 @Resource 配置依赖
@Resource 位于 javax.annotation 包下，是来自 Java EE 规范的一个 Annotation，Spring 直接借鉴了该 Annotation，通过使用该 Annotation 为目标 Bean 指定协作者 Bean  

@Resource 有一个 name 属性，在默认情况下，Spring 将这个值解释为需要被注入的 Bean 实例的 id。换句话说，使用 @Resource 与 <property> 元素的 ref 属性有相同的效果  

```
@Component
public class Chinese implements Person{
    private Axe axe;
    //axe的 setter 方法
    @Resource(name="stoneAxe")
    public void setAxe(Axe axe){
        this.axe=axe;
    }
    
    //实现 Person 接口的 useAxe() 方法
    public void useAxe(){
        //调用 axe 的 chop() 方法
        //表明 Person 对象依赖于 axe 对象
        System.out.println(axe.chop());
    }
}
```
上面的 @Resource 注解指定该 容器中的 stoneAxe 作为 setAxe() 方法的参数  

@Resource 不仅可以修饰 setter 方法，也可以直接修饰实例变量，如果使用 @Resource 修饰实例变量将会更加简单，此时 Spring 将会直接使用 Java EE 规范的 Field 注入，此时连 setter 方法都可以不要，如：
```
@Component
public class Chinese implements Person{
    @Resource(name="stoneAxe")
    private Axe axe;
    
    public void useAxe(){
        System.out.println(axe.chop());
    }
}
```
Spring 允许使用 @Resource 时省略 name 属性，当使用省略 name 属性的 @Resource 修饰 setter 方法时，name 属性默认为该 setter 方法去掉前面的 set 子串、首字母小写后得到的 子串。  

例如：使用 @Resource 标注 setName() 方法，则 Spring 默认会注入 容器中名为 name 的组件；  

当是使用省略 name 属性的 @Resource 修饰实例变量时，name 属性值默认与该实例变量同名。  

例如：使用 @Resource 标注 name 实例变量，则 Spring 默认会注入 容器中名为 name 的组件  

---

## 使用 @PostConstruct 和 @PreDestroy 定制生命周期行为  
@PostConstruct 和 @PreDestroy 同样位于 javax.annotation 包下，也是来自 Java EE 规范的两个 Annotation，Spring 直接借鉴了它们，用于定制 Spring 容器中 Bean 的生命周期行为  

Spring 生命周期提供了 <bean> 元素可以指定 init-method 、destroy-method 两个属性：
- init-method：指定 Bean 的初始化方法 —— Spring 容器将会在 Bean 的依赖关系注入完成后回调该方法
- destroy-method：指定 Bean 销毁之前的方法 —— Spring 容器将会在销毁该 Bean 之前回调该方法  


@PostConstruct 和 @PreDestroy 两个注解的作用大致与此相似，它们都用于修饰方法，无须任何属性。其中，  

前者修饰的方法是 Bean 的初始化方法  

后者修饰的方法是 Bean 销毁之前的方法  

```
@Component
public class Chinese implements Person{
    //执行 Field 注入
    @Resource(name="steelAxe")
    private Axe axe;
    
    //实现 Person 接口的 useAxe() 方法
    public void useAxe(){
        //调用 axe 的 chop() 方法
        //表明 Person 对象依赖 axe 对象
        System.out.println(axe.chop());
    }
    
    @PostConstruct
    public void init(){
        System.out.println("正在执行初始化的 init 方法...");
    }
    
    @PreDestroy
    public void close(){
        System.out.println("正在执行销毁之前的 close 方法...");
    }
}
```
上面的 Chinese 中使用了 @PostConstruct 修饰 init() 方法，这就让 Spring 在该 Bean 的依赖关系注入完成之后回调该方法；  

使用了 @PreDestroy 修饰 close() 方法，这就让 Spring 在销毁该 Bean 之前回调该方法  

---

## Spring 3.0 新增的注解
Spring 3.0 增加了两个注解:
- @DependsOn:用于强制初始化其他 Bean
- @Lazy:用于指定该 Bean 是否取消预初始化

@DependsOn 可以修饰 Bean 类或方法，使用该 Annotation 时可以指定一个字符串数组作为参数，每个数组元素对应于一个强制初始化的 Bean，如：
```
@DependsOn({"steelAxe","abc"})
@Component
public class Chinese implements Person{
    ...
}
```
上面代码使用 @DependsOn 修饰 Chinese 类，这就指定在初始化 chinese Bean 之前，会强制初始化 steelAxe、abc 两个 Bean  

@Lazy 修饰 Spring Bean 类用于指定该 Bean 的预初始化行为，使用该注解时可指定一个 boolean 型的 value 属性，该属性决定是否要预初始化该 Bean ，如：
```
@Lazy(true)
@Component
public class Chinese implements Person{
    ...
}
```
指定当 Spring 容器初始化时，不会预初始化 chinese Bean  

---

