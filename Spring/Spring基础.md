## 使用Spring管理Bean
Spring核心容器的理论很简单：Spring核心容器就是一个超级大工厂，所有的对象(包括数据源、Hibernate SessionFactory等基础性资源)都会被当成Spring核心容器管理的对象——Spirng把容器中的一切对象统称为Bean  

对于Spring框架而言，一切Java对象都是Bean  

A对象需要调用B对象方法的情形，被称为依赖  

使用Spring框架之后，Spring核心容器是整个应用中的超级大工厂，所有的Java对象都交给Spring容器管理——这些Java对象被统称为Spring容器中的Bean  

### 如何管理Bean
现在的问题是：Spring容器怎么知道管理哪些Bean呢  

答案是XML配置文件、注解配置或Java配置  

Spring可以把“一切Java对象”当成容器中的Bean，因此不管该Java类是JDK提供的，还是第三方框架提供的，抑或是开发者自己是实现的，只要是个Java类，并将它配置好，Spring容器就可以管理它  

