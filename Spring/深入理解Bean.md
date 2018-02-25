# 深入理解Bean
## 抽象Bean与子Bean
为了解决
1. 配置文件臃肿
2. 后期难以修改、维护

可以创建配置模板，即 抽象Bean  

抽象Bean 不能被实例化，Spring 容器不会创建 抽象Bean 实例。抽象Bean 的价值在于被继承，抽象Bean 通常作为 父Bean 被继承  

抽象Bean 只是配置信息的模板，指定 abstract="true" 即可阻止 Spring 实例化该 Bean，因此 抽象Bean 可以不指定 class 属性  

```
<bean id="personTemplate" abstract="true">
    <property name="name" value="ca"/>
    <property name="axe" ref="steelAxe"/>
</bean>
```

```
<bean id="chinese" class="org.crazyit.Chinese" parent="personTemplate"/>

<bean id="american" class="org.crazit.American" parent="personTemplate"/>
```

在配置文件中，chinese 和 american Bean 都指定了 parent="personTemplate"，表明这两个 Bean 都可从父 Bean 那里继承得到配置信息——虽然这两个 Bean 都没有直接指定 <property> 子元素，但它们都会从 personTemplate 模板那里继承得到两个 <property> 子元素  

---

## Bean 继承与 Java 继承的区别
Spring 中的 Bean 继承与 Java 中的继承截然不同。  

前者是实例与实例之间 参数值 的延续，对象与对象之间的关系  

后者是 一般 到 特殊 的细化，类与类之间的关系  

---

## 容器中的工厂 Bean
此处的 工厂Bean 与实例工厂方法创建 Bean，或者 静态工厂方法创建 Bean 不同：

前者 是标准的工厂模式，Spring 只是负责调用工厂方法来创建 Bean 实例  

此处的 工厂Bean，是 Spring 的一种特殊 Bean，这种工厂Bean 必须实现 FactoryBean 接口  

实现 FactoryBean 接口的最大的作用在于：  
Spring 容器并不是简单地返回该 Bean 的实例，而是返回该 Bean 实例的 getObject() 方法的返回值，而 getObject() 方法则由开发者负责实现，这样开发者希望 Spring 返回什么，只要按规律重写 getObject() 方法即可  

Spring 容器会自动检测容器中的所有 Bean，如果发现某个 Bean 实现类实现了 FactoryBean 接口，Spring 容器就会在实例化该 Bean、根据 <property> 执行 setter 方法之后，额外调用该 Bean 的 getObject() 方法，并将该方法的返回值作为容器中的 Bean  

> 在 Bean id 前增加 & 符号，这将会让 Spring 返回 FactoryBean 本身  

---


## 获得 Bean 本身的id
当程序员在开发一个 Bean 类时，该 Bean 何时被部署到 Spring 容器中，部署到 Spring 容器时所指定的 id 是什么，开发该 Bean 类的程序员无法提前预知  

此时，借助 Spring 提供的 BeanNameAware 接口，通过该接口即可提前预知该 Bean 的配置 id   

BeanNameAware 接口提供一个方法：setBeanName(String name)，该方法的 name 参数就是 Bean 的id，实现该方法的 Bean 类就可通过该该方法来部署该 Bean 的 id 了  

> BanNameAware 接口中的 setBeanName(Stirng name) 方法与 BeanFactoryAware、ApplicationContextAware 两个接口中的 setter 方法一样，这个 setter 方法不是由程序员来调用的，该方法由 Spring 容器负责调用——当 Spring 容器调用这个 setter 方法时，会把部署该 Bean 的id 属性作为参数传入  


Spring 容器会检测容器中的所有 Bean，如果发现某个 Bean 实现了 BeanNameAware 接口，Spring 容器就会在创建该 Bean 之后，自动调用该 Bean 的 setBeanName() 方法，调用该方法时，会将该 Bean 的配置 id 作为参数传给该方法 —— 该方法的实现部分将 Spring 传入的参数 (Bean 的配置 id) 赋给对象的 beanName 实例变量  

---

## 强制初始化 Bean
Spring 容器再返回 Bean 实例之前，先要完成 Bean 依赖关系的注入   

此时，如果有些存在依赖关系的 Bean 没有被初始化就要被注入的话，会抛出异常  

为了防止这种情况的发生，即为了显式指定被依赖 Bean 在目标 Bean 之前初始化，可以使用 depends-on 属性  

depends-on属性，该属性可以在初始化主调 Bean 之前，强制初始化一个或多个 Bean  

---

# 容器中Bean的生命周期 
Spring 可以管理 singleton 作用域的 Bean 的生命周期，Spring 可以精确地知道该 Bean 何时被创建、何时被初始化完成、容器何时准备销毁该 Bean 实例  

但是，对于 prototype 作用域的 Bean，Spring 容器仅仅负责创建，当容器创建了 Bean 实例之后，Bean 实例完全交给客户端代码管理，容器不再跟踪其生命周期  

每次客户端请求 prototype 作用域的 Bean 时，Spring 都会产生一个新的实例，Spring 容器无从知道它曾经创建了多少个 prototype 作用域的 Bean，也无从知道这些 prototype 作用域的 Bean 什么时候才会被销毁  

因此，Spring 无法管理 prototype 作用域的 Bean  

---


