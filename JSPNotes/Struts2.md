# MVC思想及其优势
MVC并不是Java语言特有的设计思想，也并不是Web应用所特有的思想，它是所有面向对象程序设计语言都应该遵守的规范  
MVC思想将一个应用分成三个基本部分：Model(模型)、View(视图)、Controller(控制器)，这三个部分以最少的耦合协同工作，从而提高应用的可扩展性及维护性  
MVC有如下特点：
- 多个视图可以对应一个模型。可以减少代码的复制及代码的维护量，一旦模型发生改变，也易于维护  
- 模型返回的数据与显示逻辑分离。模型可以应用任何的显示技术，例如，使用JSP页面、Velocity模板或者直接产生Excel文档等  
- 应用被分隔为三层，降低各层之间的耦合，提高应用的扩展性  
- 控制层的概念也很有效，由于它把不同的模型和不同的视图组合在一起，完成不同的请求。因此，控制层可以说是包含了用户请求权限的概念  
- MVC更符合软件工程化管理的精神。不同的层各司其职，每一层的组件具有相同的特征，有利于通过工程化和工具化产生管理程序代码  
  
对于Web应用而言，即使将多个JSP页面注册给一个模型，当模型发生变化时，模型无法主动发送消息给JSP页面(因为Web应用都是基于请求/响应模式的)，只有当用户请求浏览该页面时，控制器才负责调用模型数据来更新JSP页面。  
  
  
# Struts2的流程
Struts2推荐把所有的视图页面存放在WEB-INF目录下，这样可以保护视图页面，避免直接向视图页面发送请求。  
下面struts.xml文件中配置了一个name="*"的<action../>，该元素可以处理所有的请求，处理规则是：对于任意请求，直接呈现WEB-INF/content目录下同名的JSP页面  
如请求地址为loginForm(不要加.jsp)，Struts2会调用WEB-INF/content目录下的loginForm.jsp呈现视图  
  
## Struts2应用的开发步骤
### 1.在web.xml文件中定义核心Filter来拦截用户请求
由于Web应用是基于请求/响应架构的应用，所以不管哪个MVC Web框架，都需要在web.xml文件中配置该框架的核心Servlet或Filter，这样才可以让该框架介入Web应用中  
例如，开发Struts2应用第一步就是在web.xml文件中增加如下配置片段  
```
<!--定义Struts2的核心Filter-->
<filter>
    <filter-name>struts2</filter-name>
    <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
</filter>
<!--让Struts2的核心Filter拦截所有请求-->
<filter-mapping>
    <filter-name>struts2</filter-name>
    <urll-pattern>/*</url-pattern>
</filter-mapping>
```
### 2.提交方式
如果需要以POST方式提交请求，则定义包含表单数据的JSP页面。如果仅仅是以GET方式发送请求，则无须经过这步  
### 3.定义处理用户请求的Action类
这一步是所有MVC框架中必不可少的，因为这个Action就是MVC中的C，也就是控制器，该控制器负责调用Model里的方法来处理请求。  
MVC框架的底层机制是：
- 核心Servlet或Filter接收到用户请求后，通常会对用户请求进行简单预处理，例如解析、封装参数等  
- 然后通过反射来创建Action实例，并调用Action的指定方法(Struts1通常是execute、Struts2可以是任意方法)来处理用户请求。  
  
那当Servlet或Filter拦截用户请求后，它如何知道创建哪个Action的实例呢?
- 利用配置文件：例如配置login.action对应使用LoginAction类。这就可以让MVC框架知道创建哪个Action的实例了  
- 利用约定：约定xxx.action总是对应xxxAction类。如果核心控制器收到regist.action请求后，将会调用RegistAction类来处理用户请求  
  
在MVC框架中，控制器实际上由两个部分共同组成，即拦截所有用户请求，处理请求的通用代码都由核心控制器完成，而实际业务控制(诸如调用Model，返回处理结果等)则由Action完成   

### 4.配置Action
对于Java领域的绝大部分MVC框架而言，都非常喜欢使用XML文件来配置管理。现在Struts2的Convention插件借鉴了Rails框架的有点，开始支持“约定优于配置”的思想  
### 5.配置处理结果和物理视图资源之间的对应关系
当Action处理用户请求结束后，通常会返回一个处理结果(通常使用简单的字符串就可以了)，可以认为该名称就是逻辑视图名，这个逻辑视图名和需要指定物理视图资源关联才有价值。所以还需要配置处理结果之间的对应关系  
  
### 6.编写视图资源
如果Action需要把一些数据传给视图资源，则可以借助OGNL表达式  
  
经过上面6个步骤，即可基本完成一个Struts2处理流程的开发，也就是执行一次完整的请求->响应过程  
  
## Struts2的流程
StrutsPrepareAndExecuteFilter和XxxAction共同构成了Struts2的控制器，常常把StrutsPrepareAndExecuteFilter称为核心控制器，把XxxAction称为业务控制器  
XxxAction业务控制器通常并不与物理视图关联，这种做法提供了很好的解耦。  
业务控制器只负责返回处理结果，而该处理结果与怎样的视图关联，依然由StrutsPrepareAndExecuteFilter来决定。  
这样做的好处是，如果有一天，需要将某个视图名映射到不同的视图资源，这就无须修改XxxAction的代码，而只是修改配置文件即可  
在Struts2框架的控制下，用户请求不再向JSP页面发送，而是由核心控制器StrutsPrepareAndExecuteFilter“调用”JSP页面来生成响应，此处的调用并不是直接调用，而是将请求forward到指定JSP页面  
