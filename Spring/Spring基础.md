## 使用Spring管理Bean
Spring核心容器的理论很简单：Spring核心容器就是一个超级大工厂，所有的对象(包括数据源、Hibernate SessionFactory等基础性资源)都会被当成Spring核心容器管理的对象——Spirng把容器中的一切对象统称为Bean  

对于Spring框架而言，一切Java对象都是Bean  

A对象需要调用B对象方法的情形，被称为依赖  

