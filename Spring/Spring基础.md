## 使用Spring管理Bean
**Spring** 核心容器的理论很简单：**Spring核心容器就是一个超级大工厂**，所有的对象(包括数据源、Hibernate SessionFactory等基础性资源)都会被当成Spring核心容器管理的对象——**Spirng把容器中的一切对象统称为Bean**  

对于 Spring 框架而言，一切 Java 对象都是 **Bean**  

A对象需要调用B对象方法的情形，被称为 **依赖**  

使用Spring框架之后，Spring核心容器是整个应用中的超级大工厂，所有的Java对象都交给Spring容器管理——这些Java对象被统称为 Spring 容器中的 Bean  

### 如何管理Bean
现在的问题是：Spring 容器怎么知道管理哪些 Bean 呢  

答案是 **XML配置文件**、**注解配置** 或 **Java配置**  

Spring可以把“一切Java对象”当成容器中的Bean，因此不管该Java类是JDK提供的，还是第三方框架提供的，抑或是开发者自己是实现的，只要是个Java类，并将它配置好，Spring容器就可以管理它  

实际上，配置文件中的<bean/>元素默认以 **反射方式** 来调用该类的 **无参数的构造器**，以如下元素为例：
```
<bean id="person" class="org.crazyit.app.service.Person"/>
```
Spring框架解析该<bean/>元素后将可以得到两个字符串，其中idStr的值为“person”(解析<bean/>元素的id属性得到的值)，classStr的值为“org.crazyit.app.service.Person”(解析<bean/>元素的class属性得到的值)  

也就是说，Spring 底层会执行如以下格式的代码：
```
String idStr=..;//解析<bena/>元素的id属性得到该字符串的值为“person”
String classStr=...;//解析class属性得到该字符串值为“org.crazyit.app.service.Person”
Class clazz=Class.forName(classStr);
Object obj=clazz.newInstance();
//container代表Spring容器
container.put(idStr,obj);
```
上面代码就是最基本的反射代码(实际上Spring底层代码会更加完善一些)，Spring框架通过反射根据<bean/>元素的class属性指定的类名创建了一个Java对象，并以<bean/>元素的id属性的值为key，将该对象放入Spring容器中——这个Java对象就成为了Spring容器中的 Bean  

每个<bena/>元素默认驱动Spring调用该类的无参数的构造器来创建实例，并将该实例作为 Spring 容器中的 **Bean** 

如果配置文件中还包括一个<property>子元素，则它驱动Spring在底层以反射执行一次setter方法。其中<property>的name属性值决定执行哪个setter方法，而value或ref决定执行setter方法的传入参数   
- 如果传入参数是基本类型及其包装类、String等类型，则使用 value 属性指定传入参数  
- 如果以容器中其他Bean作为传入参数，则使用 ref 属性指定传入参数  

Spring框架只要看到<property>子元素，Spring框架就会在底层以反射方式执行一次 **setter** 方法  

何时执行这个setter方法呢?该Bean一旦创建处理，Spring会立即根据<property>子元素来执行 setter 方法。  

也就是说，<bean>元素驱动Spring调用构造器创建对象；<property>子元素驱动Spring执行setter方法，这两步是先后执行的，中间几乎没有任何间隔  

以如下配置文件为例：
```
<bean id="person" class="org.crazyit.app.service.Person">
<property name="axe" ref="axe"/>
</bean>
```
上面配置中<property>元素的name属性值为axe，该元素将驱动Spring以反射方式执行 person Bean的setAxe()方法；ref属性值为axe，该属性值指定以容器中名为 axe 的 Bean 作为执行 setter 方法的传入参数  

<hr/>

接下来程序就可通过Spring容器来访问容器中的Bean，ApplicationContext是Spring容器最常用的接口，该接口有如下两个实现类  
- **ClassPathXmlApplicationContext**：从类加载路径下搜索配置文件，并根据配置文件来创建Spring容器  
- **FileSystemXmlApplicationContext**：从文件系统的相对路径或绝对路径下去搜索配置文件，并根据配置文件来创建Spring容器  

对于Java项目而言，类加载路径总是稳定的，因此通常总是使用 **ClassPathXmlApplicationContext** 创建 Spring容器  

下面是本示例的主程序代码  
```
public class BeanTest{
    public static void main(String[] args) throws Exception{
        //创建Spring容器
        ApplicationContext ctx=new ClassPathXmlApplicationContext("beans.xml");
        //获取id为person的Bean
        Person p=ctx.getBean("person",Person.class);
        //调用userAxe()方法
        p.userAxe();
    }
}
```
上面程序中第1行代码创建了Spring容器，第2行代码通过Spring容器获取id为person的Bean——Spring容器中的Bean，就是 Java对象  

获得Bean(即Java对象)之后，即可通过该对象来调用方法、访问实例变量(如果访问权限允许)——总之，原来怎么使用Java对象，现在还怎么使用它  

可以看出，使用Spring框架之后最大的改变之一是：  
**程序不再使用new调用构造器创建Java对象**，所有的Java对象都由 **Spring容器负责创建**  
  
  
## Spring的核心机制：依赖注入
正如在前面代码中所看到的，程序代码并没有主动为Person对象的 axe 成员变量设置值，但执行 Person 对象的 userAxe() 方法时， userAxe() 方法完全可以正常访问到 Axe 对象，并可以调用 Axe 对象的 chop()方法  

由此可见，Person对象的 axe 成员变量并不是程序主动设置的，而是由 Spring容器负责设置的——开发者主要为 axe 成员变量提供一个 setter 方法，并通过 <property>元素驱动 Spring容器调用该 setter 方法为 Person对象的 axe 成员变量设置值  

纵观所有的 Java应用，这些应用中大量存在 A 对象需要调用 B 对象方法的情形，这种情形被 Spring 称为 **依赖**，即 **A 对象依赖 B 对象**。  

对于 Java应用而言，它们总是由一些互相调用的对象构成的，Spring 把这种互相调用的关系称为 **依赖关系**。  

加入 A 组件调用了 B 组件的方法，即可称 A 组件依赖 B 组件  

Spring 框架的核心功能有两个
- Spring 容器作为超级大工厂，负责创建、管理所有的 Java 对象，这些 Java 对象被称为 Bean 
- Spring 容器管理容器中 Bean 之间的依赖关系， Spring 使用一种被称为“**依赖注入**”的方式来管理 Bean 之间的依赖关系  

使用 依赖注入 ，不仅可以为 Bean 注入普通的属性值，还可以注入其他 Bean 的引用  

**通过这种 依赖注入 ，JavaEE 应用中的各种组件不需要以硬编码方式耦合在一起，甚至无需使用工厂模式**

依赖注入 达到的效果，非常类似于传说中的 “共产主义” ，当某个 Java 实例需要其他 Java 实例时，系统自动提供所需要的实例，无需程序显示获取  

依赖注入 是一种优秀的 **解耦方式**  

**依赖注入 让 Spring 的 Bean 以配置文件组织在一起，而不是以硬编码的方式耦合在一起**  

## 理解依赖注入
虽然 Spring 并不是 依赖注入 的首创者，但 Rod Johnson 是第一个高度重视以配置文件来管理 Java 实例的写作关系的人，他给这种方式起了一个名字： 控制反转(Inversion of Control，IoC)。在后来的日子里，Martine Fowler 为这种方式起了一个名称：依赖注入(Dependency Injection)  

因此，不管是 **依赖注入**，还是 **控制反转**，其**含义完全相同**。当某个 Java 对象(调用者)需要调用另一个 Java 对象(被依赖对象)的方法时，在传统模式下通常有如下两种做法  
- **原始做法**：调用者 主动 创建依赖对象，然后再调用被依赖对象的方法  
- **简单工厂模式**：调用者先找到被依赖对象的工厂，然后 主动 通过工厂去获取被依赖对象，最后再通过调用被依赖对象的方法  

对于第一种方式，由于调用者需要通过形如 “new 被依赖对象构造器();” 的代码创建对象，因此必然导致调用者与被依赖对象实现类的硬编码耦合，非常不利于项目升级的维护  

对于 简单工厂 的方式，大致需要把握三点：
1. **调用者面向被依赖对象的接口编程**  
2. **将被依赖对象的创建交给工厂完成**
3. **调用者通过工厂来获得被依赖组件** 

**通过这三点改造，可以保证调用者只需与被依赖对象的接口耦合，这就避免了类层次的硬编码耦合**  

这种方式唯一的 **缺点** 是，调用组件需要 主动 通过工厂去获取被依赖对象，这就会带来 调用组件 与 被依赖对象工厂 的耦合  

使用 Spring 框架之后，调用者无须 主动 获取 被依赖对象，调用者只要 **被动** 接收 Spring 容器为调用者的成员变量赋值即可 (只要配置一个<property>子元素，Spring 就会执行对应的 setter 方法为调用者的成员变量赋值)  

由此可见，使用 Spring 框架之后，调用者获取被依赖对象的方式由原来的 主动 获取，变成了 **被动接受**——于是 Rod Johnson 将这种方式称为 **控制反转**  

从 **Spring 容器的角度**来看，Spring 容器负责将被依赖对象赋值给调用者的成员变量——相当于为调用者注入它依赖的实例，因此 Martine Fowler 将这种方式称为 **依赖注入**  

正因为 Spring 将被依赖对象注入给了调用者，所以调用者无须 主动 获取被依赖对象，只要被动等待 Spring 容器注入即可  

由此可见，控制反转 和 依赖注入 其实是**同一个行为的两种表达**，只是**描述的角度不同**而已  

<hr/>

为了更好地理解依赖注入，可以参考人类社会的发展来看以下问题在各种社会形态里如何解决：一个人（Java实例，调用者）需要一把斧头（Java实例 被依赖对象）  

在原始社会里，几乎没有社会分工。需要斧头的人（调用者）只能自己去磨一把斧头（被依赖对象）。对应的情形为：Java程序里的调用者自己创建被依赖对象，通常采用new 关键字调用构造器创建一个被依赖对象，这是Java初学者经常感到事情。

进入工业社会，工厂出现了，斧头不再由普通人完成，而在工厂里被生产出来，此时需要斧头的人（调用者）找到工厂，购买斧头，无需关心斧头的制造过程。对应简单工厂设计模式，调用者只需要定位工厂，无须理会被依赖对象的具体实现过程。

进入 “共产主义” 社会，需要斧头的人甚至不需要定位工厂，“坐等” 社会提供即可。调用者无须关心被依赖对象的实现，无须理会工厂，等待Spring依赖注入。

在第一种情况下，Java实例的调用者创建被调用的Java实例，调用者直接使用 new 关键字创建被依赖对象，程序高度耦合，效率低下。真实应用极少使用这种方式。

这种模式是Java初学者最喜欢使用的方式，这种模式有如下两个坏处。
- 可扩展性差。由于“人”组件与“斧头”组件的实现类高度耦合，当程序视图扩展斧头组件时，“人”组件的代码也要随之改变。

- 各组件职责不清。对于“人”组件而言，它只需要调用“斧头”组件的方法即可，并不关心“斧头”组件的创建过程。但在这种模式下，“人”组却需要主动创建“斧头”组件，因此职责混乱

如果经常采用这种方式创建 Java 对象，说明你对 Java 掌握得相当不够  

第二种情况下，调用者无须关心被依赖对象的具体实现过程，只需要找到符合某种标准（接口）的实例，即可使用。此时调用者的代码面向接口编程，可以让调用者和被依赖对象的实现类解耦，这也是工厂模式被大量使用的原因。  

但调用者依然需要主动定位工厂，调用者与工厂耦合在一起。  

第三种情况是最理想的情况：程序完全无须理会被依赖对象的具体实现，也无须主动定位工厂，这是一种优秀的解耦方式，实例之间的依赖关系由IoC容器负责管理。

由此可见，使用Spring框架之后的两个主要改变是：
- 程序无须使用new 调用构造器去创建对象。所有的Java对象都可以交给Spring容器去创建。  
- 当调用者需要调用被依赖对象的方法时，调用者无须主动获取被依赖对象，只要等待Spring容器注入即可

依赖注入通常有如下两种
- **设值注入**：IoC容器使用成员变量的setter方法来注入被依赖对象
- **构造注入**：IoC容器使用构造器来注入被依赖对象  


## 设值注入
设值注入是指 IoC 容器通过成员变量的 setter 方法来注入被依赖对象  

这种注入方式简单、直观，因而在 Spring 的依赖注入里大量使用  

Spring 推荐 面向接口编程  

不管是调用者，还是被依赖对象，都应该为之定义接口，程序应该面向它们的接口，而不是面向实现类编程，这样以便程序后期的升级、维护  

下面先定义一个 Person 接口，该接口定义了一个 Person 对象应遵守的规范  
```
public interface Person{
    //定义一个使用斧头的方法
    public void useAxe();
}
```
下面是Axe接口的代码
```
public interface Axe{
    //Axe接口中定义一个chop()方法
    public String chop();
}
```

Spring 推荐面向接口编程，这样可以更好地让规范和实现分离，从而提供更好的解耦。  

对于一个 JavaEE 应用，不管是 DAO 组件，还是业务逻辑组件，都应该先定义一个接口，该接口定义了该组件应该实现的功能，但功能的实现则由其实现类提供  

下面是 Person 实现类的代码
```
public class Chinese implements Person{
    private Axe axe;
    //设值注入所需的 setter 方法
    public void setAxe(Axe axe){
        this.axe=axe;
    }
    //实现 Person 接口的 useAxe()方法
    public void useAxe(){
        //调用 axe 的 chop() 方法
        //表明 Person 对象依赖于 axe 对象
        System.out.println(axe.chop());
    }
}
```
上面代码实现了 Person 接口的 useAxe() 方法，实现该方法时调用了 axe 的 chop() 方法，这就是典型的 依赖关系  

在上面的 Chinese 类中，Chinese 类并不知道它要调用的 axe 实例在哪里，也不知道 axe 实例是如何实现的，它只是需要调用 Axe 对象的方法，这个 Axe 实例将由 Spring 容器负责注入  

下面提供一个 Axe 的实现类：StoneAxe
```
public class StoneAxe implements Axe{
    public String chop(){
        return "石斧砍柴好慢";
    }
}
```
到此为止，程序依然不知道 Chinese 类和哪个 Axe 实例耦合， Spring 当然也不知道，Spring 需要使用 XML 配置文件来指定实例之间的依赖关系  

