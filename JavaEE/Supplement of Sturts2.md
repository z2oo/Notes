## Struts2使用注解问题
想要在struts2中使用注解需要导入struts2-convention-plugin-xx.jar包  
并且还要导入asm、asm-commons和asm-tree包，否则会报错java.lang.NoClassDefFoundError: org/objectweb/asm/ClassVisitor  
  
## struts.xml配置
```
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE struts PUBLIC
		"-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
		"http://struts.apache.org/dtds/struts-2.5.dtd">
<struts>
	<package name="user" namespace="/" extends="struts-default">
		<action name="Login">
			<result>WEB-INF/content/login.jsp</result>
		</action>
		<action name="Welcome" class="action.WelcomeUserAction">
			<result name="SUCCESS">/WEB-INF/content/welcome_user.jsp</result>
		</action>
		
		<action name="*">
			<result>/WEB-INF/content/{1}.jsp</result>
		</action>
	</package>
</struts>
```
上面struts.xml文件中配置了一个name="*"的<action../>，该元素可以处理所有的请求，处理规则是：  
对于任意请求，直接呈现WEB-INF/content目录下**同名**的JSP页面  
所以如果请求地址为loginForm(不添加.jsp)，Struts2会调用WEB-INF/content目录下的loginForm.jsp呈现视图

  
## @ResultPath注释示例
在Struts 2中, @ResultPath 注解用于控制Struts2找到存储的结果或JSP页面。默认情况下，它会找到结果页在 “WEB-INF/content/” 文件夹。  
### 默认结果路径
在登录动作类,设置 “/User” 作为命名空间, 并重定向到 “pages/login.jsp” 页面。  
P.S 假设struts2example是上下文servlet名称  
```
@Namespace("/User")
@Result(name="success",location="pages/login.jsp")
public class LoginAction extends ActionSupport{
}
```
访问它，如下：  
```
http://localhost:8080/struts2example/User/login.action
```
Struts 2将从默认位置找到“login.jsp”结果页面：  
```
/struts2example/WEB-INF/content/User/pages/login.jsp
```
  
### 定制结果路径
如果JSP结果页面存储在其他位置，那么可以使用 @ResultPath注释设置改变它。  
```
@Namespace("/User")
@ResultPath(value="/")
@Result(name="success",location="pages/login.jsp")
public class LoginAction extends ActionSupport{
}
```
再一次访问：  
```
http://localhost:8080/struts2example/User/login.action
```
现在Struts2将从不同的位置找到“login.jsp”结果页面：
```
/Struts2Example/User/pages/login.jsp

```
### 全局@ResultPath
@ResultPath只适用于类级别。在全局范围内应用它，可以在 struts.xml 文件中进行配置。  
```
<struts>
	<constant name="struts.convention.result.path" value="/"/>
</struts>
```
  
  
## namespace
namespace属性跟访问网页的URL有关，即在网页中访问项目的的路径有关，而与实际的路径无关，举例：
```
<package name="user" namespace="/pag" extends="struts-default">
		<action name="Login">
			<result>/login.jsp</result>
		</action>
		<action name="Welcome" class="action.Wel">
			<result name="SUCCESS">/WEB-INF/pages/welco2.jsp</result>
		</action>
	</package>
```
我的项目的结构：
web  
|——WEB-INF  
|　　　　|——content  
|　　　　　　　　|——welco.jsp  
|　　　　|——pages  
|　　　　　　　　|——welco2.jsp  
|　　　　|——web.xml  
|——index.jsp  
|——login.jsp
  
然后，struts.xml中配置的namespace为'/pag'，可以看得出跟这个项目结构没有半点关系，注意<result>/login.jsp</result>这个路径前面是需要加`/`符号的，代表需要切换路径  
通常情况下，我们在浏览器的地址栏输入地址
```
localhost:8080/Login.action
```
结果报错了
```
There is no Action mapped for namespace [/] and action name [Welcome] associated with context path [].
```
接下来，我们继续输入
```
localhost:8080/Welcom.actioin
```
同样是报相同的错误  
  
现在，我们换一种方式来访问  
输入
```
localhost:8080/pag/Login.action
```
页面显示成功了  
可以看出namespace在页面访问的URL中才是起作用的，namespace使得Action可以在不同的命名空间之间同名 
它允许以模块化的方式来组织Action  

  
    
      
## 多个Struts配置文件
在Struts2，应该给每个模块一个Struts配置文件。在这种情况下，可以创建三个文件：
- audit-struts.xml – 将所有审计模块设置在这里。
- user-struts.xml – 将所有用户模块设置在这里。
- struts.xml – 默认设置，包含 struts-audit.xml 和 Struts-user.xml 两个文件。  
  
struts.xml
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
"-//Apache Software Foundation//DTD Struts Configuration 2.0//EN"
"http://struts.apache.org/dtds/struts-2.0.dtd">
 
<struts>
 
<package name="default" namespace="/" extends="struts-default">
</package>

<include file="conf/user-struts.xml"></include>
<include file="conf/audit-struts.xml"></include>

</struts>
```
在struts.xml配置文件中引入另外两个配置文件即可   
这样struts.xml文件分成更小的模块相关的部分   
  
    
      
## Struts2开发模式
为了启用 Struts 2 的开发模式，可以通过自动配置显著增加Struts2的开发速度和属性文件加载，以及额外的日志和调试功能。  
  
> 自动重新加载功能真的是一个方便的功能。每次修改属性或XML配置文件更改，应用程序不再需要重启才能生效。  

默认情况下，Struts2的开发模式是禁用的  
### 启用Struts2开发模式
将“struts.devMode”的值设置为true，可以在Struts的属性文件或XML配置文件。  
struts.properties
```
struts.devMode=true
```
struts.xml
```
<struts>
    <constant name="struts.devMode" value="true"/>
</struts>
```
> 开发模式只适合于开发和调试环境。在生产环境中，你必须禁用它。因为整个应用程序的配置它会引起对性能显著影响，属性文件将在每次请求重新加载，许多额外的日志和调试信息也将提供。

