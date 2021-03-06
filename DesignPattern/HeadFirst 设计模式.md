# HeadFirst 设计模式

## 1.策略模式
**策略模式**——定义了算法族，分别封装起来，让它们之间可以互相替换，此模式让算法的变化独立于使用算法客户。  

![](http://on-img.com/chart_image/5ae30853e4b04f3db58981db.png)  

## 2.观察者模式
**观察者模式**——定义了对象之间的一对多依赖，这样一来，当一个对象改变状态时，它的所有依赖者都会受到通知并自动更新。  

![](http://on-img.com/chart_image/5a1d7828e4b0b3ee057531b6.png)   


## 3.装饰者模式
**装饰者模式**——动态地将责任附加到对象上。若要扩展功能，装饰者提供了比继承更有弹性的替代方案。  

![](http://on-img.com/chart_image/5ae31c86e4b090f6eeb8dbb1.png)  
  
  
![](http://on-img.com/chart_image/5ae31fc8e4b019d3a9143408.png)  

## 4.简单工厂
简单工厂其实不是一个设计模式，反而比较像是一种编程习惯。

![](http://on-img.com/chart_image/5ae32281e4b04f3db589c6a2.png)    


## 5.工厂方法模式
**工厂方法模式**——定义了一个创建对象的接口，但由子类决定要实例化的类是哪一个。工厂方法让类把实例化推迟到子类。  

工厂方法模式能够封装具体类型的实例化。抽象的 Creator 提供了一个创建对象的方法的接口，也称为“工厂方法”。  

在抽象的 Creator 中，任何其它实现的方法，都有可能使用到这个工厂方法所制造出来的产品，但只有子类真正实现这个工厂方法并创建产品。  

![](http://on-img.com/chart_image/5ae32651e4b039625af382b2.png)  

![](http://on-img.com/chart_image/5ae32a7ce4b039625af38bd0.png)  

## 6.抽象工厂模式
**抽象工厂模式**——提供一个接口，用于创建相关或依赖对象的家族，而不需要明确指定具体类。  

抽象工厂允许客户使用抽象的接口来创建一组相关的产品，而不需要知道 (或关心) 实际产出的具体产品是什么。  

抽象工厂的方法经常以工厂方法的方式实现。  

抽象工厂的任务是定义一个负责创建一组产品的接口。这个接口内的每个方法都负责创建一个具体产品，同时我们利用实现抽象工厂的子类来提供这些具体的做法。  

当你需要创建产品家族和想让制造的相关产品集合起来时，可以使用抽象工厂。  

当你目前还不知道将来需要实例化哪些具体类时，可以通过工厂方法来实现。使用时，只要把工厂方法继承成子类，并实现工厂方法就可以。  


![](http://on-img.com/chart_image/5ae330e5e4b019d3a9145bd7.png)  

![](http://on-img.com/chart_image/5ae33860e4b039625af3a90c.png)  

## 7.单例模式
**单例模式**——确保一个类只有一个实例，并提供一个全局访问点。  

![](http://on-img.com/chart_image/5ae489dce4b039625af67f52.png)  

## 8.命令模式
**命令模式**——将“请求”封装成对象，以便使用不同的请求、队列或者日志来参数化其他对象。命令模式也支持可撤销的操作。  

![](http://on-img.com/chart_image/5ae5306be4b04f3db58d12ac.png)  

![](http://on-img.com/chart_image/5ae53334e4b039625af6ca20.png)    


## 9.适配器模式
**适配器模式**——将一个类的接口，转换成客户期望的另一个接口。适配器让原来接口不兼容的类可以合作无间。  

![](http://on-img.com/chart_image/5ae54f85e4b0411f64d8b4a0.png)  

## 10.外观模式
**外观模式**——提供了一个统一的接口，用来访问子系统中的一群接口。外观定义了一个高层接口，让子系统更容易使用。  

![](http://on-img.com/chart_image/5ae55311e4b090f6eebc6da5.png)  


## 11.模板方法模式
**模板方法模式**——在一个方法中定义一个算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以再不改变算法结构的情况下，重新定义算法中的某些步骤。  

![](http://on-img.com/chart_image/5ae5af81e4b039625af78142.png)  
  
  
## 12.迭代器模式
**迭代器模式**——提供一种方法顺序访问一个聚合对象中的各个元素，而又不暴露其内部的表示。  

![](http://on-img.com/chart_image/5ae5d673e4b090f6eebd29a0.png)  

## 13.组合模式
**组合模式**——允许你将对象组合成树形结构来表现“整体/部分”层次结构。组合能让客户以一致的方式处理个别对象以及对象组合。  

![](http://on-img.com/chart_image/5ae9aeeae4b019d3a91ed286.png)  



## 14.状态模式
**状态模式**——允许对象在内部状态改变时改变它的行为，对象看起来好像修改了它的类。  

![](http://on-img.com/chart_image/5ae9b459e4b04f3db5946ea8.png)  


## 15.代理模式
**代理模式**——为另一个对象提供一个替身或占位符以控制对这个对象的访问。  

使用代理模式创建代表对象，让代表对象控制某对象的访问，被代理的对象可以是远程的对象、创建开销大的对象或需要安全控制的对象。  

![](http://on-img.com/chart_image/5ae9bae2e4b0411f64dff15f.png)  

