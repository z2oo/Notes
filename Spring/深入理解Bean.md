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
Spring 可以管理 singleton 作用域的 Bean 的生命周期，Spring 可以精确地知道该 Bean 何时被创建、何时被初始化完成、容器何时d准备销毁该 Bean 实例  

但是，对于 prototype 作用域的 Bean，Spring 容器仅仅负责创建，当容器创建了 Bean 实例之后，Bean 实例完全交给客户端代码管理，容器不再跟踪其生命周期  

每次客户端请求 prototype 作用域的 Bean 时，Spring 都会产生一个新的实例，Spring 容器无从知道它曾经创建了多少个 prototype 作用域的 Bean，也无从知道这些 prototype 作用域的 Bean 什么时候才会被销毁  

因此，Spring 无法管理 prototype 作用域的 Bean  

管理 Bean 的生命周期行为主要有如下两个时机：
- 注入依赖关系之后
- 即将销毁 Bean 之前

---

## 依赖关系注入之后的行为
Spring 提供两种方式在 Bean 全部属性设置成功后执行特定行为：
- 使用 init-method 属性
- 实现 InitializingBean 接口

第一种，init-method 属性 指定某个方法应在 Bean 全部依赖关系设置结束后自动执行  
>使用这种方式不需要将代码与 Spring 的接口耦合在一起，代码污染小

第二种，让 Bean 类实现 InitializingBean 接口，该接口提供一个方法 —— void afterPropertiesSet() throws Exception;  

> 实现 InitializingBean 接口污染了代码，是侵入式设计，不推荐采用

---

## Bean 销毁之前的行为
两种方式：
- 使用 destroy-method 属性
- 实现 DisposableBean 接口，destroy 方法

用法同上  

singleton 作用域的 Bean 通常会随容器的关闭而销毁，但问题是：ApplicationContext 容器再什么时候关闭呢?  

> - 在基于 Web 的ApplicationContext 实现中，系统已经提供了相应的代码保证关闭 Web 应用时恰当地关闭 Spring 容器
> - 如果处于非 Web 应用的环境下，为了让 Spring 容器优雅地关闭，并调用 singleton Bean 上的相应析构回调方法，则需要在 JVM 里注册一个关闭钩子 (shutdown hook) ，这样就可保证 Spring 容器被恰当关闭，且自动执行 singleton Bean 实例的析构回调方法  

---

## 协调作用域不同步的 Bean
依赖关系分为 
- singleton 作用域的 Bean 之间存在依赖关系  
- prototype 作用域的 Bean 依赖 singleton 作用域的 Bean  
- singleton 作用域的 Bean 依赖 prototype 作用域的 Bean  

在最后一种关系中：
> Spring 容器会在初始化 singleton 作用域的 Bean 之前，先创建被依赖的 prototype Bean，然后才初始化 singleton Bean，并将 prototype Bean 注入 singleton Bean，这会导致以后无论何时通过 singleton Bean 去访问 prototype Bean 时，得到的永远是最初的那个 prototype Bean —— 这样就相当于 singleton Bean 把它所依赖的 prototype Bean 变成了 singleton 行为  

这样，就产生了不同步的现象，有两种思路：
- 放弃依赖注入：singleton 作用域的 Bean 每次需要 prototype 作用域的 Bean 时，主动向容器请求新的 Bean 实例，即可保证每次注入的 prototype Bean 实例都是最新的实例  
- 利用方法注入

第一种，代码主动请求新的 Bean 实例，导致程序代码与 Spring API 耦合，造成代码污染  

第二种方法，使用方法注入，通常使用  

方法注入通常使用 lookup 方法注入，使用 lookup 方法注入可以让 Spring 容器重写容器中 Bean 的抽象或具体方法，返回查找容器中其他 Bean 的结果，被查找的 Bean 通常是一个 non-singleton Bean ，Spring 通过使用 JDK 动态代理或 cglib 库修改客户端的二进制码，从而实现上述要求  

为了使用 lookup 方法注入分为如下两步：
> 1. 将调用者 Bean 的实现类定义为抽象类，并定义一个抽象方法来获取被依赖的 Bean  
> 2. 在 <bean> 元素中添加 <lookup-method> 子元素让 Spring 为调用者 Bean 的实现类实现指定的抽象方法


Spring 会采用运行时动态增强的方式来实现 <lookup-method> 元素所指定的抽象方法，如果目标抽象类实现过接口，Spring 会采用 JDK 动态代理来实现该抽象类，并为之实现抽象方法；如果目标抽象类，没有实现过接口，Spring 会采用 cglib 实现该抽象类，并为之实现抽象方法  

使用 lookup-method 方法注入后，系统每次调用 getDog() 方法时都将生成一个新的实例，这样就可以保证当 singleton 作用域的 Bean 需要 prototype Bean 实例时，直接调用 getDog() 方法即可获取全新的实例，从而避免一直使用最早注入的 Bean 实例  

---
# 高级依赖关系配置
> Spring 框架的本质是，开发者在 Spring         配置文件中使用 XML 元素进行配置，实际驱动 Spring 执行相应的代码。例如：
> - 使用<bean> 元素，实际启动 Spring 执行无参数或有参数的构造器，或者调用工厂方法创建 Bean
> - 使用<property> 元素，实际驱动 Spring 执行一次 setter 方法  

> 但 Java 程序还可能有其他类型的语句，如调用 getter 方法、调用普通方法、访问类或对象的 Field，而 Spring 也为这种语句提供了对应的配置语法
> - 调用 getter 方法：使用 PropertyPathFactoryBean
> - 访问类或对象的 Field 值：使用FieldRetrievingFactoryBean
> - 调用普通方法：使用 MethodInvokingFactoryBean

---
## 获取其他 Bean 的属性值
PropertyPathFactoryBean 用来获取目标 Bean 的属性值 (实际上就是它的 getter 方法的返回值)，获得的值可注入给其他 Bean，也可直接定义成新的 Bean  

```
<!-- 将指定 Bean 实例的 getter 方法返回值定义成 son1 Bean -->
<bean id="son1" class="org.springframework.beans.factory.config.PropertyPathFactoryBean">
    <!-- 确定目标 Bean，指定 son1 Bean 来自哪个 Bean 的 getter 方法-->
    <property name="targetBeanName" value="person"/>
    <!-- 指定 son1 Bean 来自目标Bean 的哪个 getter 方法，son 代表getSon() -->
    <property name="propertyPath" value="son"/>
</bean>
```
两个 <property> 子元素指定了获取 person 的 getSon() 方法的返回值，该返回值将直接定义成容器中的 son1 Bean  

> PropertyPathFactoryBean 就是工厂 Bean，工厂Bean 专门返回某个类型的值，并不是返回该 Bean 的实例，在这种配置下，配置 PropertyPathFactoryBean 工厂 Bean 时指定的 id 属性，并不是该 Bean 的唯一标识，而是用于指定属性表达式的值

Spring 获取指定 Bean 的 getter 方法的返回值之后，该返回值不仅可直接定义成容器中的 Bean 实例，还可注入另一个 Bean  ：
```
<!-- 下面定义 son2 Bean-->
<bean id="son2" class="org.crazyit.app.Service.Son">
    <property name="age">
        <!-- 使用嵌套 Bean 为调用 setAge() 方法指定参数值 -->
        <!-- 以下是访问指定 Bean 的 getter 方法的简单方式，person.son.age 代表 person.getSon().getAge() -->
        <bean id="person.son.age" class="org.springframework.beans.factory.config.PropertyPathFactoryBean"/>
    </property>
</bean>
```
程序调用 son2 实例的 setAge() 方法时的参数并不是直接指定的，而是将容器中另一个 Bean 实例的属性值 (getter 方法的返回值) 作为 setAge() 方法的参数，PropertyPathFactoryBean 工厂 Bean 负责获取容器中另一个 Bean 的属性值( getter 方法的返回值 )  

---
## 获取Field 值
```
<bean id="son" class="org.crazyit.app.Son">
    <property name="age">
        <!-- 将 java.sql.Connection的 TRANSACTION_SERIALIZABLE 的值作为调用 setAge() 的参数 -->
        <bean id="java.sql.Connection.TRANSACTION_SERIALIZABLE" class="org.springframework.beans.factory.config.FieldRetrievingFactoryBean"/>
    </property>
</bean>
```
son 的 age 成员变量的值，等于 java.sql.Connection 接口中 TRANSACTION_SERIALIZABLE 的值。  

在上面的定义中，定义 FieldRetrievingFactoryBean 工厂 Bean 时指定的 id 属性，并不是该 Bean 实例的唯一标识，而是指定 Field 表达式  

---

# 基于 XML Schema 的简化配置方式
## 使用 p:命名空间简化配置
当导入 p: 命名空间之后，就可直接在 <bean> 元素中使用属性来驱动执行 setter 方法  
```
<beans ...
    xmlns:p="http://www.springframework.org/schema/p"
    ...>
    <bean id="chinese" class="org.crazyit.app.service.impl.Chinese" p:age="29" p:axe-ref="stoneAxe"/>
    <bean id="steelAxe" class="org.crazyit.service.impl.SteelAxe"/>
</beans>
```
---
## 使用 c: 命名空间简化配置
p:命名空间主要用于简化设值注入，而 c: 命名空间则用于简化构造注入  

```
<bean id="chinese" class="org.crazyit.app.service.impl.Chinese" c:axe-ref="steelAxe" c:age="29"/>
<bean id="steelAxe" class="org.crazyit.app.service.impl.SteelAxe"/>
```

