## Bean
从本质上看，Spring 容器就是一个超级大工厂，Spring 容器中的 Bean 就是该工厂的产品  

Spring 容器能产生哪些产品，则完全取决于开发者在配置文件中的配置  

---
对于开发者来说，开发者使用 Spring 框架主要是做两件事：
- ①开发 Bean
- ②配置 Bean

对于 Spring 框架来说，它要做的就是根据配置文件来创建 Bean 实例，并调用 Bean 实例的方法完成 “依赖注入”——这就是所谓 IoC 的本质  

这就要求开发者在使用 Spring 框架时，眼中看到的是“XMl配置”，心中想的是“Java 代码”  

> 注意  
Spirng 框架的本质就是，通过 XML 配置来驱动 Java 代码，这样就可以把原本由 Java 代码管理的耦合关系，提取到 XML 配置文件中管理，这就实现了系统中各组件的解耦，有利于后期的升级和维护  

### Bean的基本定义
<beans>元素是 Spring 配置文件的根元素，该元素可以指定如下属性：
- default-lazy-init：指定该 <beans> 元素下配置的所有 Bean 默认的延迟初始化行为  
- default-merge：指定该<beans>元素下配置的所有 Bean 默认的 merge 行为
- default-autowire：指定该<beans>元素下配置的所有 Bean 默认的自动装配行为
- default-autowire-candidates：指定该<beans>元素下配置的所有 Bean 默认是否作为自动装配的候选 Bean 
- default-init-method：指定该<beans>元素下配置的所有 Bean 默认的初始化方法
- default-destroy-method：指定该<beans>元素下配置的所有 Bean 默认的回收方法

> <beans>元素下所能指定的属性都可以在每个<bean>子元素中指定——将属性名去掉 default 即可

 <bean> 元素是 <beans> 元素的子元素，<beans> 元素可以包含多个 <bean> 子元素，每个 <bean> 子元素定义一个 Bean，每个 Bean 对应 Spring 容器里的一个 Java 实例  
 
 定义 Bean 时，通常需要指定两个属性
 - id：确定该 Bean 的唯一标识，容器对 Bean 的管理、访问，以及该 Bean 的依赖关系，都通过该属性完成。Bean 的 id 属性在 Spring 容器中应该是唯一的  
 - class：指定该 Bean 的具体实现类，这里不能是接口。Spring 容器必须知道创建该 Bean 的实现类，而不能是接口。在通常情况下，Spring 会直接使用 new 关键字创建该 Bean 的实例，因此，这里必须提供 Bean 的实现类的类名
 
在某些情况下， Bean 的标识必须包含某些特殊符号，此时可采用 name 属性，用于指定 Bean 的别名，通过访问 Bean 别名也可以访问 Bean 实例  

### 容器中Bean的作用域
当通过 Spring 容器创建一个 Bean 实例时，不仅可以完成 Bean 实例的实例化，还可以为 Bean 指定特定的作用域  

Spring支持如下5种作用域：
- singleton：单例模式，在整个 Spring IoC 容器中，singleton 作用域的 Bean 将只生成一个实例  
- prototype：每次通过容器的 getBean() 方法获取 prototype 作用域的 Bean 时，都将产生一个新的 Bean 实例
- request：对于一次 HTTP 请求，request 作用域的 Bean 将只生成一个实例，这意味着，在同一次 HTTP 请求内，程序每次请求该 Bean，得到的总是同一个实例。只有在 Web 应用中使用 Spring 时，该作用域才真正有效
- global session：每个全局的 HTTP Session 对应一个 Bean 实例。在典型情况下，仅在使用 portlet context 的时候有效。只有在 Web 应用中使用 Spring 时，该作用域才真正有效

比较常用的是 singleton 和 prototype 两种作用域，对于 singleton 作用域的 Bean，每次请求该 Bean 都将获得相同的实例，**容器负责跟踪 Bean 实例的状态，负责维护 Bean 实例的声明周期行为**

如果一个 Bean 被设置成 prototype 作用域，程序每次请求该 id 的 Bean，Spring 都会新建一个 Bean 实例，然后返回给程序。在这种情况下，Spring 容器仅仅使用 new 关键字创建 Bean 实例，一旦创建成功，**容器不再跟踪实例，也不会维持 Bean 实例的状态**  

如果不指定 Bean 的作用域，Spring 默认使用 singleton 作用域  

Java 在创建 Java 实例时，需要进行内存申请；销毁实例时，需要完成垃圾回收，这些工作都会导致系统开销的增加。  

因此，prototype 作用域的 Bean 的创建、销毁代价比较大。而 singleton 作用域的 Bean 实例一旦创建成功，就可以重复使用。因此，应该尽量避免将 Bean 设置成 prototype 作用域  

> 早期指定 Bean 的作用域也可通过 singleton 属性指定，该属性只接受两个属性值：true 和 false，分别代表 singleton 和 prototype 作用域，singleton 属性是 Spring 1.2.x的方式

通常会将 Web 应用的控制器 Bean 指定成 request 作用域  

request 和 session 作用域只在 Web 应用中才有效，并且必须在 Web 应用中增加额外配置才会生效   

为了让 request 和 session 作用域生效，必须将 HTTP请求对象绑定到为该请求提供服务的线程上，这使得具有 request 和 session 两个作用域的 Bean 实例能够在后面的调用链中被访问到  

对于支持 Servlet2.4 及更新规范的 Web 容器，可以在 Web 应用的 web.xml 文件中增加如下的 Listener 配置，该 Listener 负责使 request 作用域生效  
```
<listener>
    <listener-class>
    org.springframework.web.context.request.RequestContextListener</listener-class>
</listener>
```

> 如果 Web 应用直接使用 Spring MVC 作为 MVC 框架，即用 SpringDispatcherServlet 或 DispatcherPortlet 来拦截所有用户请求，则无需这些额外的配置，因为 SpringDispatcherServlet 和 DispatcherPortlet 已经处理了所有和请求有关的状态处理  

### 配置依赖
Java 应用中各组件相互调用的实质可以归纳为 依赖关系，根据注入的方式的不同，Bean 的依赖注入通常有如下两种形式
- 设值注入：通过<property>元素驱动 Spring 执行 setter 方法
- 构造注入：通过<constructor-arg>元素驱动 Spring 执行带参数的构造器

不管是设置注入，还是构造注入，都视为 Bean 的依赖，接受 Spring 容器管理，依赖关系的值要么是一个确定的值，要么是 Spring 容器中其他 Bean 的引用  

对于 singleton 作用域的 Bean，如果没有强行取消其预初始化行为，系统会在创建 Spring 容器时预初始化所有的 singleton Bean，与此同时，该 Bean 所依赖的 Bean 也被一起实例化  
BeanFactory 与 ApplicationContext 实例化容器中的 Bean 的时机不同：
- BeanFactory：等到程序需要 Bean 实例时才创建 Bean
- ApplicationContext：在容器创建 ApplicationContext 实例时，会预初始化容器中所有的 singleton Bean  

创建 BeanFactory 时不会立即创建 Bean 实例，所以有可能程序可以正确地创建 BeanFactory 实例，**但当请求 Bean 实例时依然抛出一个异常：创建 Bean 实例或注入它的依赖关系时出现错误**  

配置错误的延迟出现，也会给系统引入不安全因素，而 ApplicationContext 则默认预初始化所有 singleton 作用域的 Bean，所以 ApplicationContext 实例化过程比 BeanFactory 实例化过程的时间和内存开销大，但**可以在容器初始化阶段就检验处配置错误**  

Spring 允许通过如下元素为 setter 方法、构造器参数指定参数值
- value
- ref
- bean
- list、set、map及props

### 设置普通属性值
<value>元素用于指定基本类型及其包装、字符串类型的参数值，Spring 使用 XML 解析器来解析出这些数据，然后利用 java.beans.PropertyEditor完成类型转换：从 java.lang.String 类型转换为所需的参数值类型。如果目标类型是基本类型及其包装类，通常都可以正确转换

### 配置合作者Bean
如果需要为 Bean 设置的属性值是容器中的另一个 Bean 实例，则应该使用 <ref> 元素。使用 <ref> 元素时可指定一个 bean 属性，该属性用于引用容器中其他 Bean 实例的 id 属性值  

通过为 <property> 元素增加 ref 属性，可以将容器中另一个 Bean 作为调用 setter 方法的参数  

### 使用自动装配注入合作者Bean
Spring 能自动装配 Bean 与 Bean 之间的依赖关系，即无须使用 ref 显式指定依赖 Bean，而是由 Spring 容器检查 XML 配置文件内容，根据某种规则，为调用者 Bean 注入被依赖的 Bean  

Spring 的自动装配可以通过 <beans> 元素的 default-autowire 属性指定，该属性对配置文件中所有的 Bean 起作用；也可通过 <bean>元素的 autowire 属性指定，该属性只对该 Bean 其作用  

自动装配可以减少配置文件的工作量，但降低了依赖关系的透明性和清晰性  

autowire、default-autowire 属性可以接受如下值：
- **no**：不使用自动装配。Bean 依赖必须通过 ref 元素定义。这是默认的配置，在较大的部署环境中不鼓励改变这个配置，显式配置合作者能够得到更清晰的依赖关系  
- **byName**：根据 setter 方法名进行自动装配。Spring 容器查找容器中的全部 Bean，找出其 id 与 setter 方法名去掉 set 前缀，并小写首字母后同名的 Bean 来完成注入。如果没有找到匹配的 Bean 实例，则 Spring 不会进行任何注入  
- **byType**：根据 setter 方法的形参类型来自动装配。Spring 容器查找容器中的全部 Bean，如果正好有一个 Bean 类型与 setter 方法的形参类型匹配，就自动注入这个 Bean；如果找到多个这样的 Bean，就抛出一个异常；如果没找到这样的 Bean，则什么都不会发生，setter 方法不会被调用

> 由此可见，byName 策略是根据 setter 方法的方法名与 Bean 的 id 进行匹配；byType 策略是根据 setter 方法的参数类型与 Bean 的类型进行匹配

- **constructor**：与 byType 类似，区别是用于自动匹配构造器的参数。如果容器不能恰好找到一个与构造器参数类型匹配的 Bean，则会抛出一个异常
- **autodetect**：Spring 容器根据 Bean 内部结构，自行决定使用 constructor 或 byType 策略。如果找到一个默认的构造函数，那么就会应用 byType 策略

#### byName规则
byName 规则是指 setter 方法的方法名与 Bean 的 id 进行匹配，假如 Bean A 的实现类包含 setB() 方法，而 Spring 的配置文件恰好包含 id 为 b 的 Bean，则 Spring 容器会将 b 实例注入 Bean A 中。如果容器中没有名字匹配的 Bean，Spring 则不会做任何事情  

#### byType规则
byType 规则是根据 setter 方法的参数类型与 Bean 的类型进行匹配。假如 A 实例有 setB(B b) 方法，而 Spring 的配置文件中恰好有一个类型为 B 的 Bean 实例，包括B 的子类型实例，则容器为 A 注入类型匹配的 Bean 实例，如果容器中没有类型为 B 的实例，Spring 不会调用 setB() 方法；但如果容器中包含多于一个的 B 实例，程序将会抛出异常  

---
当一个 Bean 既使用自动装配策略，又使用 ref 显示指定依赖时，则显式指定的依赖覆盖自动装配依赖

> 对于大型应用，不鼓励使用自动装配。虽然使用自动装配可减少配置工作的工作量，但大大降低了依赖关系的 清晰性 和 透明性。依赖关系的装配依赖于源文件的属性名或属性类型，导致 Bean 与 Bean 之间的耦合降低到了代码层次，不利于高层次解耦  

在某些情况下，程序希望将某些 Bean 排除在自动装配之外，不作为 Spring 自动装配策略的候选者，此时可设置 autowire-candidate 属性，通过为 <bean> 元素设置 autowire-candidate="false"，即可将该 Bean 排除在自动装配之外，容器在查找自动装配 Bean 时将不考虑该 Bean  


### 注入嵌套Bean
如果某个 Bean 所依赖的 Bean 不想被 Spring 容器直接访问，则可以使用嵌套 Bean  

把 <bean> 配置成 <property> 或 <constructor-args> 的子元素，那么该 <bean> 元素配置的 Bean仅仅作为 setter 注入、构造注入的参数，这种 Bean 就是嵌套 Bean。由于容器不能获取嵌套 Bean，因此它不需指定 id 属性  

```
<bean id="chinese" class="org.crazyit.app.service.impl.Chinese">
    <!-- 驱动调用 chinese 的 setAxe() 方法，使用嵌套 Bean 作为参数-->
    <property name="axe">
        <!-- 嵌套 Bean 配置的对象仅作为 setter 方法的参数，嵌套 Bean 不能被容器访问，因此无须指定 id 属性 -->
        <bean class="org.crazyit.app.service.impl.SteelAxe"/>
    </property>
</bean>
```
采用上面的配置形式可以保证嵌套 Bean 不能被容器访问，因此不用担心其他程序修改嵌套 Bean。外部 Bean 的用法与之前的用法完全一样，使用结果也没有区别  

> 注意  
嵌套 Bean 提高了程序的内聚性，但降低了程序的灵活性。只有在完全确定无须通过 Spring 容器访问某个 Bean 实例时，才考虑使用嵌套 Bean 来配置该 Bean 

**使用嵌套 Bean 与使用 ref 引用容器中的另一个 Bean 在本质上是一样的**  

Spring 框架的本质就是 **通过 XMl 配置文件来驱动 Java 代码**，当程序要调用 setter 方法或有参数的构造器时，程序总需要传入参数值，随参数类型的不同，Spring 配置文件当然也要随之改变
- 形参类型是基本类型、String、日期等，直接使用 value 指定字面值即可  
- 形参类型是复合类(如 Person、Dog、DataSource等)，那就需要传入一个 Java 对象作为实参，于是有两种方式：
    - ①使用 ref 引用一个容器中已配置的 Bean(Java 对象)
    - ②使用 <bean> 元素配置一个嵌套 Bean(Java 对象)

### 注入集合值
如果需要调用形参类型为集合的 setter 方法，或调用形参类型为集合的构造器，则可使用集合元素 <list>、<set>、<map>和<props> 分别来设置类型为 List、Set、Map 和 Properties 的集合参数值  

```
<list>
    <!-- 每个 value、ref、bean...都配置一个 List 元素 -->
    <value>小学</value>
    <value>中学</value>
    <value>大学</value>
</list>
```
```
<map>
    <!-- 每个 entry 都配置一个 key-value 对-->
    <entry key="数学" value="87"/>
    <entry key="英语" value="90"/>
</map>
```
```
<props>
    <!-- 每个prop 元素都配置一个属性项，其中 key 指定属性名-->
    <prop key="血压">正常</prop>
    <prop key="身高">175</prop>
</props>
```
```
<set>
    <!-- 每个value、ref、bean..都配置一个Set 元素-->
    <value>普通的字符串</value>
    <bean class="org.crazyit.app.service.impl.SteelAxe"/>
    <ref bean="stoneAxe"/>
    <!-- 为Set 集合配置一个 List 集合作为元素-->
    <list>
        <value>20</value>
        <!-- 再次为 List 集合配置一个 Set 集合作为元素-->
        <set>
            <value type="int">30</value>
        </set>
    </list>
</set>
```
<props> 元素用于配置 Properties 类型的参数值，Properties类型是一种特殊的类型，其 key 和 value 都只能是字符串，故 Spring 配置 Properties 类型的参数值比较简单：**每个key-value 对只要分别给出 key 和 value 就足够了——而且key 和 value 都是字符串类型**，所以使用 如下格式的 <prop> 元素就足够了
- <prop key="血压">正常</prop>,key 属性指定 key 的值，<prop>元素的内容指定 value 的值

Spring 还提供了一个简化语法来支持 Properties 形参的 setter 方法，例如：
```
<property name="health">
    <value>
        pressure=normal
        height=175
    </value>
</property>
```

然而上面的配置方式有一个很大的限制：属性名、属性值都只能是英文、数字，不可出现中文  

从 Spring 2.0 开始，Spring IoC 容器将支持集合的合并，子 Bean 中的集合属性值可以从其父 Bean 的集合属性继承和覆盖而来。也就是说，子 Bean 的集合属性的最终值是 父 Bean、子 Bean 合并后的最终结果，而且子 Bean 集合中的元素可以覆盖父 Bean 集合中对应的元素  

下面的配置片段示范了集合合并的特性：
```
<beans>
    <!--将父Bean 定义成抽象 Bean-->
    <bean id="parent" abstract="true" class="example.ComplexObject">
        <!--定义 Properties 类型的集合属性-->
        <property name="adminEmails">
            <props>
                <prop key="administrator">administrator@crazyit.org</prop>
                <prop key="support">support@crazyit.org</prop>
            </props>
        </property>
    </bean>
</beans>
```
上面配置片段中 child Bean 继承了 parent Bean，并为 <props>元素指定了 merge="true"，这将会把parent Bean 的集合属性合并到 child Bean中；当进行合并时，由 child Bean 再次配置了名为 support 的属性，所以该属性将会覆盖 parent Bean 中的配置定义，于是 child Bean 的 adminEmails 属性值将变为自定义的  

## Spring的 Bean 和 JavaBean
Spring 容器对 Bean 没有特殊的要求，甚至不要求该 Bean 像标准的 JavaBean——必须为每个属性提供对应的 getter 和 setter 方法  

Spring 中的 Bean 是 Java 实例、Java 组件；而传统 Java 应用中的 JavaBean 通常作为 DTO(数据传输对象)，用来封装值对象，在各层之间传递数据  

Spring 中的 Bean 比 JavaBean 的功能要复杂，用法也更丰富。  

当然，传统的 JavaBean 也可作为 Spring 的 Bean，从而接受 Spring 的管理  

虽然 Spring 对 Bean 没有特殊要求，但依然建议 Spring 中的 Bean 应满足如下几个规则
- 尽量为每个 Bean 实现类提供无参数的构造器
- 接受构造注入的 Bean，则应提供对应的、带参数的构造函数
- 接受设值注入的 Bean，则应提供对应的 setter ，并不要求提供对应的 getter 方法

传统的 JavaBean 和 Spring 中的 Bean存在如下区别
- 用处不同：传统的 JavaBean 更多是作为值对象传递参数；Spring 的 Bean 用处几乎无所不包，任何应用组件都被称为 Bean  
- 写法不同：传统的 JavaBean 作为值对象，要求每个属性都提供 getter 和 setter 方法；但 Spring 的 Bean 只需接受设值注入的属性提供 setter 方法即可
- 生命周期不同：传统的 JavaBean 作为值对象传递，不接受任何容器管理其生命周期；Spring 中的 Bean 由 Spring 管理其生命周期行为

