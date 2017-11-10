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
	<package name="user" namespace="/User" extends="struts-default">
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

