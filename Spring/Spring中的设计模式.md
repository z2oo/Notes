# 常见设计模式

设计模式的定义：
> 设计模式是对处于特定环境下，经常出现的某类软件开发问题的，一种相对成熟的设计方案  

设计模式分为三类：
- 创建型：创建对象时，不再直接实例化对象；而是根据特定场景，由程序来确定创建对象的方式，从而保证更高的性能、更好的架构优势  
    - 主要有：简单工厂模式、工厂方法、抽象工厂模式、单例模式、生成器模式和原型模式

- 结构型：用于帮助将多个对象组织成更大的结构
    - 主要有：适配器模式、桥接模式、组合器模式、装饰器模式、门面模式、享元模式和代理模式
- 行为型：用于帮助系统间各对象的通信，以及如何控制复杂系统中的流程
    - 主要有：命令模式、解释器模式、迭代器模式、中介者模式、备忘录模式、观察者模式、状态模式、策略模式、模板模式和访问者模式
---

## 单例模式
如果一个类时钟只能创建一个实例，则这个类被称为单例类，这种模式被称为单例模式  

对于 Spring 框架来说，配置 Bean 实例时指定 scope="singleton" 来配置单例模式，默认是单例模式  

Spring 推荐将所有的业务逻辑组件、DAO组件、数据源组件等配置成单例的行为方式   

因为这些组件无须保存任何用户状态，故所有客户端都可共享这些业务逻辑组件、DAO组件，因此推荐将这些组件配置成单例模式的行为方式  

单例模式的优势：
1. 减少创建 Java 实例所所带来的系统开销
2. 便于系统跟踪单个 Java 实例的生命周期、实例状态等

---

## 简单工厂
例如 A 实例调用 B 实例的方法  

如果使用 new 关键字来创建一个 B 实例，然后调用 B 实例的方法，A 类的方法实现直接调用了 B 类的类名 ( 这种方法也被称为硬编码耦合 )  

一旦系统需要重构：需要使用 C 类来代替 B 类时，程序不得不改写 A 类代码，如果应用中有 100 个 类以硬编码的方式耦合 B 类，则需要重新改写 100 个地方

对于 A 对象而言，它只需调用 B 对象的方法，并不关心 B 对象的实现、创建过程  

考虑让 B 类实现一个 IB 接口，而 A 类只需要 IB 接口耦合 —— A 类并不直接使用 new 关键字来创建 B 实例，而是重新定义一个工厂类：IBFactory，由该工厂类来负责创建 IB 实例；而 A 类通过调用 IBFactory 工厂的方法来得到 IB 的实例  

通过改用这种设计，则 A 类不但需要与 IBFactory 耦合，还需要与 IB 接口耦合  

如果系统需要重构：需要使用 C 类代替 B 类  

则只需要让 C 类也实现 IB 接口，并改写 IBFactory 工厂中创建 IB 实例的实现代码，让该工厂产生 C ( 实现了 IB 接口 ) 实例即可。由于所有依赖 IB 实例的对象都是通过工厂来获取 IB 实例的，所以它们都将改为获得 C 实例，这就完成了系统重构  

使用简单工厂的优势：
- 让对象的调用者和对象创建过程分离，当对象调用者需要对象时，直接向工厂请求即可；从而避免了对象的调用者与对象的实现类以硬编码方式耦合，以提高系统的可维护性、可扩展性  

缺陷：
- 当产品修改时，工厂类也需要进行相应的修改  

对 Spring 容器而言，它首先是一个巨大的工厂，它负责创建所有 Bean 实例，整个应用的所有组件都由 Spring 容器负责创建  

不仅如此，Spring 容器扩展了这种简单工厂模式，他还可以管理 Bean 实例之间的依赖关系  

而且，如果容器中 Bean 实例具有 singleton 行为特征，则 Spring 容器还会缓存该 Bean实例，从而保证程序通过 Spring 工厂来获取该 Bean 实例时，Spring 工厂将会返回一个 Bean 实例  

---
## 工厂方法和抽象工厂
在简单工厂模式里，系统使用工厂类生产所有产品实例，且该工厂决定生产哪个类的实例，即该共产类负责所有的逻辑判断、实例创建等工作   

如果不想在工厂类中进行逻辑判断，程序可以为不同产品类提供不同的工厂，不同的工厂类生产不同的产品  

例如，例如可以为 Printer、BetterPrinter 分别提供 PrinterFactory 和 BetterPrinterFactory 工厂类，这就无须在工厂类进行复杂的逻辑判断  


```
public interface OutputFactory{
    //仅定义一个方法用于返回输出设备
    Output getOutput();
}
```

```
public class PrinterFactory implements OutputFactory{
    public Output getOutput(){
        //该工厂只负责产生 Printer 对象
        return new Printer();
    }
}
```

```
public class BetterFactory implements OutputFactory{
    public Output getOutput(){
        //该工厂只负责产生 BetterFacotory 对象
        return new BetterFactory();
    }
}
```

![](http://on-img.com/chart_image/5aa4e568e4b0d77c8a82a684.png)  


当使用工厂方法设计模式时，对象调用者需要与具体的工厂类耦合：  

当需要不同对象时，程序需要调用响应工厂对象的方法来得到所需的对象  

如下是 Computer 类中创建 Output 对象并调用该对象方法的代码  

```
public class Computer{
    private Output out;
    
    public Computer(Output out){
        this.out=out;
    }
    
    //定义一个模拟获取字符串输入的方法
    public void keyIn(String msg){
        out.getData(msg);
    }
    
    public static void main(String[] args){
        //使用 PrinterFactory 子类来创建 OutputFactory
        OutputFactory of=new PrinterFactory();
        //将 Output 对象传入，创建 Computer 对象
        Computer c=new Computer(of.getOutput());
        c.keyIn("jkl");
    }
}
```
从上面的代码可以看出，对于采用工厂方法的设计架构，客户端代码成功与被调用对象的实现类分离  

但，带来了另一种耦合：客户端代码与不同的工厂类耦合  

为了解决客户端代码与不同的工厂类耦合的问题，接着考虑再增加一个工厂类，该工厂类不是生产 Output 对象，而是生产 OutputFactory 实例   

简而言之，这个工厂类不制造具体的被调用对象，而是制造不同的工厂对象，这个特殊的工厂类被称呼为抽象工厂类，设计方式为抽象工厂模式  

![](http://on-img.com/chart_image/5aa4ebdbe4b0fb5a6bf0bcca.png)  


```
public class OutputFactoryFactory{
    //仅定义一个方法用于返回输出设备
    public static OutputFactory getOutputFactory(String type){
        if(type.equalsIgnoreCase("better"){
            return new BetterPrinterFactory();
        }else{
            return new PrinterFactory();
        }
    }
}
```
可看出，抽象工厂根据 type 参数进行判断，决定生成哪种工厂实例，通过这种设计模式，就可以让客户端程序只需与抽象工厂类耦合  

```
public class void main(String[] args){
    //使用 PrinterFactory 子类来创建 OutputFactory
    OutputFactory of=OutputFactoryFactory.getOutputFactory("better");
    //调用 OutputFactory 的方法获取 Output对象
    //并将 Output 对象传入 Computer中
    Computer c=new Computer(of.getOutput());
}