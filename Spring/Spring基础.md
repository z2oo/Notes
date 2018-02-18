## 使用Spring管理Bean
Spring核心容器的理论很简单：Spring核心容器就是一个超级大工厂，所有的对象(包括数据源、Hibernate SessionFactory等基础性资源)都会被当成Spring核心容器管理的对象——Spirng把容器中的一切对象统称为Bean  

对于Spring框架而言，一切Java对象都是Bean  

A对象需要调用B对象方法的情形，被称为依赖  

使用Spring框架之后，Spring核心容器是整个应用中的超级大工厂，所有的Java对象都交给Spring容器管理——这些Java对象被统称为Spring容器中的Bean  

### 如何管理Bean
现在的问题是：Spring容器怎么知道管理哪些Bean呢  

答案是XML配置文件、注解配置或Java配置  

Spring可以把“一切Java对象”当成容器中的Bean，因此不管该Java类是JDK提供的，还是第三方框架提供的，抑或是开发者自己是实现的，只要是个Java类，并将它配置好，Spring容器就可以管理它  

实际上，配置文件中的<bean/>元素默认以反射方式来调用该类的无参数的构造器，以如下元素为例：
```
<bean id="person" class="org.crazyit.app.service.Person"/>
```
Spring框架解析该<bean/>元素后将可以得到两个字符串，其中idStr的值为“person”(解析<bean/>元素的id属性得到的值)，classStr的值为“org.crazyit.app.service.Person”(解析<bean/>元素的class属性得到的值)  

也就是说，Spring底层会执行如以下格式的代码：
```
String idStr=..;//解析<bena/>元素的id属性得到该字符串的值为“person”
String classStr=...;//解析class属性得到该字符串值为“org.crazyit.app.service.Person”
Class clazz=Class.forName(classStr);
Object obj=clazz.newInstance();
//container代表Spring容器
container.put(idStr,obj);
```
上面代码就是最基本的反射代码(实际上Spring底层代码会更加完善一些)，Spring框架通过反射根据<bean/>元素的class属性指定的类名创建了一个Java对象，并以<bean/>元素的id属性的值为key，将该对象放入Spring容器中——这个Java对象就成为了Spring容器中的Bean  

每个<bena/>元素默认驱动Spring调用该类的无参数的构造器来创建实例，并将该实例作为Spring容器中的Bean 

如果配置文件中还包括一个<property>子元素，则它驱动Spring在底层以反射执行一次setter方法。其中<property>的name属性值决定执行哪个setter方法，而value或ref决定执行setter方法的传入参数   
- 如果传入参数是基本类型及其包装类、String等类型，则使用value属性指定传入参数  
- 如果以容器中其他Bean作为传入参数，则使用ref属性指定传入参数  

Spring框架只要看到<property>子元素，Spring框架就会在底层以反射方式执行一次setter方法  

何时执行这个setter方法呢?该Bean一旦创建处理，Spring会立即根据<property>子元素来执行setter方法。  

也就是说，<bean>元素驱动Spring调用构造器创建对象；<property>子元素驱动Spring执行setter方法，这两步是先后执行的，中间几乎没有任何间隔  

以如下配置文件为例：
```
<bean id="person" class="org.crazyit.app.service.Person">
<property name="axe" ref="axe"/>
</bean>
```
上面配置中<property>元素的name属性值为axe，该元素将驱动Spring以反射方式执行person Bean的setAxe()方法；ref属性值为axe，该属性值指定以容器中名为axe的Bean作为执行setter方法的传入参数  

<hr/>

接下来程序就可通过Spring容器来访问容器中的Bean，ApplicationContext是Spring容器最常用的接口，该接口有如下两个实现类  
- ClassPathXmlApplicationContext：从类加载路径下搜索配置文件，并根据配置文件来创建Spring容器  
- FileSystemXmlApplicationContext：从文件系统的相对路径或绝对路径下去搜索配置文件，并根据配置文件来创建Spring容器  

对于Java项目而言，类加载路径总是稳定的，因此通常总是使用ClassPathXmlApplicationContext创建Spring容器  

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
上面程序中第1行代码创建了Spring容器，第2行代码通过Spring容器获取id为person的Bean——Spring容器中的Bean，就是Java对象  

获得Bean(即Java对象)之后，即可通过该对象来调用方法、访问实例变量(如果访问权限允许)——总之，原来怎么使用Java对象，现在还怎么使用它  

可以看出，使用Spring框架之后最大的改变之一是：  
程序不再使用new调用构造器创建Java对象，所有的Java对象都由Spring容器负责创建  
