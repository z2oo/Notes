# JSP
JSP(Java Server Page)和Servlet是JavaEE规范的两个基本成员。  

JSP和Servlet的本质是一样的，因为JSP最终必须编译成Servlet才能运行，或者说JSP只是生成Servlet的“草稿”文件。  

Web应用应该有如下结构：　　  
  
```  
         <webDemo>----这是web应用的名称，可以改变  
    |——WEB-INF  
    |　　　　|——classes  
    |　　　　|——lib  
    |　　　　|——web.xml  
    |——<a.jsp>----此处可以存放任意多个JSP页面
```  
  
# JSP的基本原理
JSP的本质是Servlet，当用户向指定Servlet发送请求时，Servlet利用输出流动态生成HTML页面，包括每一个静态的HTML标签和所有在HTML页面中出现的内容。  

由于包括大量的HTML标签、大量的静态文本及格式等，导致Servlet的开发效率极为低下。所有的表现逻辑，包括布局、色彩及图像等，都必须耦合在Java代码中，这的确让人不胜其烦。JSP的出现弥补了各种不足。  

JSP通过在标准的HTML页面中嵌入Java代码，其静态的部分无须Java程序控制，只有那些需要从数据库读取或需要动态生成的页面内容，才使用Java脚本控制。  

JSP页面的内容由如下两部分组成：
- 静态部分：标准的HTML标签、静态的页面内容，这些内容与静态HTML页面相同。

- 动态部分：受Java程序控制的内容，这些内容由Java脚本动态生成。

  
  
  
从表面上看，JSP页面已经不再需要Java类，似乎完全脱离了Java面向对象的特征。事实上，JSP的本质仍然是Servlet（一个特殊的Java类），每个JSP页面就是一个Servlet实例——JSP页面由系统编译成Servlet，Servlet再负责相应用户请求。  

也就是说，JSP其实也是Servlet的一种简化，使用JSP时，其实还是使用Servlet，因为Web应用中的每个JSP页面都会由Servlet容器生成对应的Servlet。对于Tomcat而言，JSP页面生成的Servlet放在work路径对应的Web应用下。  
  
  
Tomcat根据JSP页面生成对应Servlet的Java文件和class文件。  

打开Java文件，会发现这就是一个Servlet类的源代码，该Java类主要包含如下三个方法：
- init():初始化JSP/Servlet的方法。
- destroy()：销毁JSP/Servlet之前的方法。
- service():对用户请求生成响应的方法。

所以，其实JSP页面的所有内容都由这个java文件的页面输出流来生成。  

## 结论
- JSP文件必须在JSP服务器内运行。

- JSP文件必须生成Servlet才能执行。
- 每个JSP页面的第一个访问者速度很慢，因为必须等待JSP编译成Servlet。
- JSP页面的访问者无须安装任何客户端，甚至不需要可以运行Java的运行环境，因为JSP页面输送到客户端的是标准HTML页面。  
  

# JSP的4中基本语法
## 1.JSP注释
JSP注释的格式：
```
<%-- 注释内容 --%>
```
HTML注释的格式：
```
<!-- HTML注释部分--!>
```
HTML的注释可以通过源代码查看到，而JSP的注释是无法通过源代码查看到的。这表明JSP注释不会被发送到客户端。  

## 2.JSP声明
JSP声明用于声明变量和方法。在JSP声明中声明方法看起来很特别，似乎不需要定义类就可以直接定义方法。 

实际上，JSP声明将会转换成对应Servlet的成员变量或成员方法，因此JSP声明依然符合java语法。  

JSP声明的语法格式如下：
```
<%! 声明部分 %>
```

> 提示：  
**由于JSP声明语法定义的变量和方法对应于Servlet类的成员变量和方法，所以JSP声明部分定义的变量和方法可以使用private、public等访问控制符修饰，也可使用static修饰，将其变成类属性和类方法。但不能使用abstract修饰声明部分的方法，因为抽象方法将导致JSP对应的Servlet变成抽象类，从而导致无法实例化。**

JSP页面会编译成一个Servlet类，每个Servlet在容器中只有一个实例；在JSP中声明的变量是成员变量，成员变量只在创建实例时初始化，该变量的值将一直保存，直到实例销毁。  

JSP声明的方法其实是JSP编译中生成的Servlet的实例方法——Java里的方法是不能独立的，即使在JSP页面中也不行。   

## 3.输出JSP表达式
语法格式：
```
<%=表达式%>
```
输出表达式的语法可以代替out.println输出语句。由此可见，输出表达式将转换成Servlet里的输出语句。  

> 注意：
**输出表达式语法后不能有分号**

## 4.JSP脚本
格式：
```
<% java代码%>
```
通常来说，所有可执行Java代码都可通过JSP脚本嵌入HTML页面。  
  
JSP脚本将转换成Servlet里Service方法的可执行性代码。这意味着在JSP小脚本部分也可以声明变量，但在JSP脚本部分声明的变量是局部变量，但不能使用private、public等访问控制符修饰，也不可使用static修饰。 
> **实际上不仅JSP小脚本部分会转换成_jspService方法里的可执行性代码，JSP页面里的所有静态内容都将由这个方法里的输出语句来输出，这就是JSP脚本可以控制JSP页面中静态内容的原因。由于JSP脚本将转换成_jspService方法里的可执行性代码，而Java语法不允许在方法里定义方法，所以JSP脚本里不能定义方法。**

因为JSP脚本中可以放置任何可执行性语句，所以可以利用Java语言功能，例如连接数据库和执行数据库操作。  


# JSP的3个编译指令
常见的编译指令有如下三个：
- page：该指令是针对当前页面的指令。
- include：用于指定包含另一个页面。
- taglib：用于定义和访问自定义标签。
  

使用编译指令的语法格式如下：
```
<%@ 编译指令名 属性名="属性值"···%>
```

## 1.page指令
page指令通常位于JSP页面的顶端，一个JSP页面可以使用多条page指令。  
page指令语法格式如下：
```
<%@ page
[ language="java" ]
[ extends="package.class" ]
[ import="{package.class | package.*}, ..." ]
[ session="true | false" ]
[ buffer="none | 8kb | sizekb" ]
[ autoFlush="true | false" ]
[ isThreadSafe="true | false" ]
[ info="text" ]
[ errorPage="relativeURL" ]
[ contentType="mimeType [ ;charset=characterSet ]" | "text/html ; charset=ISO-8859-1" ]
[ isErrorPage="true | false" ]
%>
```
个别属性意义：
- **language**：声明当前JSP页面使用的脚本语言的种类，因为页面是JSP页面，该属性的值通常是Java，该属性的默认值也是java，所以通常无须设置。  

- **extends**：指定JSP页面编译所产生的Java类所继承的父类，或所实现的接口。  
- **import**：用来导入包。不需显式导入的包有：java.lang.\*、javax.servlet.\*、javax.servlet.jsp.\*、javas.ervlet.http.*  

- **session**：设定这个JSP页面是否需要HTTP Session。  

- **buffer**：指定输出缓冲区的大小。out为输出缓冲区的JSP内部对象，用于缓存JSP页面对客户浏览器的输出，默认值为8KB。  

- **autoFlush**：当输出缓冲区即将溢出时，是否需要强制输出缓冲区的内容。true为正常输出；false则会在溢出时产生一个异常。  

- **info**：设置该JSP程序的信息。可以通过Servlet.getServletInfo()方法获取该值。如果在JSP页面中，可以直接调用getServletInfo()方法，因为JSP页面本身就是Servlet。  

- **contentType**：用于设定生成网页的文件格式和编码字符集，即MIME类型和页面字符集类型，默认的MIME类型是text/html：默认的字符集类型是ISO-8859-1.  

- **pageEncoding**：指定生成网页的编码字符集。

> 提示：
**errorPage属性的实质是JSP的异常处理机制，因为JSP內建了异常机制支持，所以JSP可以不处理异常，即使是checked异常。**


## 2.include指令
使用include指令，可以将一个外部文件嵌入到当前JSP文件中，同时解析这个页面中的JSP语句（如果有的话）。这是个静态的include语句，它会把目标页面的其他编译指令也包含进来，但动态include则不会。  

include既可以包含静态的文本，也可以包含动态的JSP页面。静态的include编译指令会将被包含的页面加入本页面，融合成一个页面，因此被包含页面甚至不是一个完整的页面。  

include指令的语法：
```
<%@include file="relativeURLSpec"%>
```
如果被嵌入的文件经常需要改变，建议使用<jsp:include>操作指令，因为它是动态的include语句。

在`staticInclude.jsp`这个页面中使用`<%@include file="scriptlet.jsp"%>`  
打开`staticInclude_jsp.java`这个文件（即staticInclude.jsp编译后的java文件）可以看到scriptlet.jsp页面的内容被完全融入staticInclude.jsp页面所生成的Servlet中，这就是静态包含意义：包含页面在编译时将完全包含了被包含页面的代码。    
> 注意：
**静态包含还会被包含页面的编译指令也包含进来，如果两个页面的编译指令冲突，那么页面就会出错。** 
  
  
 

# JSP的7个动作指令
动作指令与编译指令不同，编译指令是通知Servlet引擎的处理消息，而动作指令只是运行时的动作。 

编译指令在将JSP编译成Servlet时起作用；而处理指令通常可替换成JSP脚本，它只是JSP脚本的标准写法。  
  
JSP动作指令主要有如下7个：
- jsp:forward  执行页面转向，将请求的处理转发到下一个页面

- jsp:param  用于传递参数，必须与其他支持参数的标签一起使用
- jsp:include  用于动态引入一个JSP页面
- jsp:plugin 用于下载JavaBean或者Applet到客户端执行
- jsp:useBean 创建一个Javabean实例
- jsp:setProperty  设置JavaBean实例的属性值
- jsp:getProperty  获取JavaBean实例的属性值

## 1.forward指令
forward指令用于将页面响应转发到另外的页面。既可以转发到静态的HTML页面，也可以转发到动态的JSP页面，或者转发到容器中的Servlet。  

forward指令格式：  
对于JSP1.0，使用如下语法：
```
<jsp:forward page="{relativeURL|<%=expression%>}"/>
```
对于JSP1.1以上规范，可使用如下语法：
```
<jsp:forward page="{relativeURL|<%=expression%>}">
    {<jsp:para.../>}
</jsp:forward>
```
第二种语法用于在转发时增加额外的请求参数。增加的请求参数的值可以通过HttpServletRequest类的getParameter()方法获取。  

执行forward指令时，用户请求的地址依然没有发生改变，但页面内容却完全变为forward目标页的内容。

在JSP页面中使用request内置对象（request对象是HTTPServletRequest的实例）来获取增加的请求参数值。  

执行forward指令转发请求时客户端的请求参数不会丢失。  
JSP页面不仅可以输出forward指令增加的请求参数，还可以看到表单里的username表单域对应的请求参数，这表明forward时不会丢失请求参数。  
> 提示：
**从表面上看，<jsp:forward../>指令给人一种感觉：它是将用户请求“转发”到了另一个新页面，但实际上，<jsp:forward../>并没有重新向新页面发送请求，它只是完全采用了新页面来对用户生成响应————响应依然是一次请求，所以请求参数、请求属性都不会丢失。**  

  
## 2.include指令
include指令是一个动态include指令，也用于包含某个页面，它不会导入被include页面的编译指令，仅仅将被导入页面的body内容插入本页面。  

include指令语法：
```
<jsp:include page="{relativeURL|<%=expression%>}" flush="true"/>
```
或者
```
<jsp:include page="{relativeURL|<%=expression%>}" flush="true">
    <jsp:param name="parameterName" value="patameterValue"/>
</jsp:include>
```
flush属性用于指定输出缓存是否转移到被导入文件中。如果指定为true，则包含在被导入文件中；如果指定为false，则包含在原文件中。对于JSP1.1以前版本，只能设置为false。  

  
表面上看，include指令执行效果与使用静态include导入的页面并没有什么不同。但查看JSP页面生成的Servlet的源代码，可以看到有如下片段：
```
org.apache.jasper.runtime.JspRuntimeLibrary.include(request,response,"目标网页",out,false);
```
这段代码显示了动态导入的关键：动态导入只是使用了一个include方法来插入目标页面的内容，而不是将目标页面完全融入本页面中。  

静态导入和动态导入的三点区别：
- 静态导入是将导入页面的代码完全融入，两个页面融合成一个整体Servlet；而动态导入则在Servlet中使用include方法来引入被导入页面的内容。  

- 静态导入时被导入页面的编译指令则失去作用；而动态导入时被导入页面的编译指令则失去作用，只是插入被导入页面的body内容。
- 动态包含还可以增加额外的参数。

  
> 提示：  
**forward动作指令和include动作指令十分相似（它们的语法就很相似），它们都采用方法来引入目标页面，通过查看JSP页面所生成的Servlet代码可以得出：forward指令使用_jspx_page_context的forward()方法来引入目标页面，而include指令则使用通过JSPRuntimeLibrary的include()方法来引入目标页面。**  
**区别在于：执行forward时，被forward的页面将完全代替原有页面；而执行include时，被include的页面只是插入原有页面。**

## 3.useBean、setProperty、getProperty指令
这三个指令都是与JavaBean相关的指令。  
- useBean：用于在JSP页面初始化一个Java实例。  
- setProperty：用于为JavaBean实例的属性设置值。
- getProperty：用于输出JavaBean实例的属性。

  
如果多个JSP页面中需要重复使用某段代码，则可以把这段代码定义成Java类的方法，然后让多个JSP页面调用该方法即可。  

### useBean语法：
```
<jsp:useBean id="name" class="classname" scope="page|request|session|application"/>
```
其中，id属性是JavaBean的实例名，class属性确定JavaBean的实现类。scope属性用于指定JavaBean实例的作用范围，该范围有4个值：
- page：该JavaBean实例仅在该页面有效  
- request：该JavaBean实例仅在本次请求有效
- session：该JavaBean实例在本次session内有效
- application：该JavaBean实例在本应用内一直有效

### setProperty语法：
```
<jsp:setProperty name="BeanName" property="propertyName" value="value"/>
```
- name：确定需要设定JavaBean的实例名
- property：确定需要设置的属性名
- value：确定需要设置的属性值

### getProperty语法：
```
<jsp:getProperty name="BeanName" property="propertyName"/>
```
- name：确定需要输出的JavaBean的实例名
- property：确定需要输出的属性名

对于setProperty和getProperty而言，它们都要求根据属性名来操作JavaBean的属性，而要求的属性名，与Java类中定义的属性有一定的差别。 

事实上，当页面使用setProperty和getProperty标签时，系统底层就是调用setXXX()和getXXX()方法来操作Java类的实例的属性。  
  
  
## 4.plugin指令
plugin指令主要用于下载服务器端的JavaBean或Applet到客户端执行。由于程序在客户端执行，因此客户端必须安装虚拟机。  
> **实际由于现在很少使用Applet，而且就算要使用Applet，也完全可以使用支持Applet的HTMl标签，所以使用jsp:plugin标签的使用场景并不多。**

## 5.param指令
param指令用于设置参数值，这个指令本身不能单独使用，可以与一下三个指令结合使用：
- jsp:include
- jsp:forward
- jsp:plugin

当与include指令结合使用时，param指令用于将参数传入被导入的页面  
与forward指令结合时，param指令用于将参数值传入被转向的页面  
与plugin结合时，param指令用于将参数传入页面中的JavaBean实例或applet实例。  

param语法：
```
<jsp:param name="paramName" value="value" />
```


  
# JSP脚本中的9个内置对象
JSP脚本中包含了9个内置对象，这些对象都是Servlet API接口的实例，只是JSP规范对它进行了默认初始化（由JSP页面对应Servlet的_jspService()方法来创建这些实例）。也就是说，它们已经是对象了，可以直接使用。    
- **application**：javax.servlet.ServletContext的实例，该实例代表JSP所属的Web应用本身，可用于JSP页面，或者在Servlet之间交换信息。常用方法有getAttribute(String attName)、setAttribute(String attName,String attValue)和getInitParameter(String paramName)等  


- **config**：javax.servlet.ServletConfig的实例，该实例代表JSP的配置信息。常用的方法有getInitParameter(String paramName)和getInitParameternames()等方法。事实上，JSP页面通常无须牌子，也就不存在配置信息。因此，该对象更多地在Servlet中有效。  

- **exception**：java.lang.Throwable的实例，该实例代表其他页面中的异常和错误。只有当页面是错误处理页面，即编译指令page的isErrorPage属性为true时，该对象才可以使用。常用的方法有getMessage()和printStackTrace()等。  

- **out**：javax.servlet.jsp.JspWriter的实例，该实例代表JSP页面的输出流，用于输出内容，形成HTML页面。  

- **page**：代表页面本身，通常没有太大用处。也就是Servlet中的this，其类型就是生成Servlet类，能用page的地方就可用this。  
  
- **pageContext**：javax.servlet.jsp.PageContext的实例，该对象代表该JSP页面上下文，使用该对象可以访问页面中的共享数据。常用方法：getServletContext()和getServletConfig()等。  

- **request**：javax.servlet.http.HttpServletRequest的实例，该对象封装了一次请求，客户端的请求参数都奔封装在该对象里。这是一个常用对象，获取客户端请求参数必须使用该对象。常用方法：getParameter(String paramName)、getParameterValues(String paramName)、setAttribute(String attrName,Object attrValue)、getAttribute(String attrName)和setCharacterEncoding(String env)等。  
- **response**：javax.servlet.http.HttpServletResponse的实例，代表服务器对客户端的响应。通常很少使用该对象直接响应，而是使用out对象，除非需要生成非字符响应。而response对象常用于重定向，常用方法：getOutputStream()、sendRedirect(java.lang.String location)等。  
- **session**：javax.servlet.http.HttpSession的实例，该对象代表一次回话。当客户端浏览器与站点建立连接时，回话开始；当客户端关闭浏览器时，回话结束。常用方法：getAttribute(String attrName)、setAttribute(String attrName,Object attrValue)等。  


几乎所有的JSP页面编译后Servlet类都有如下所示的结构：
```
public void _jspService( HttpServletRequest request, HttpServletResponse response)
throws java.io.IOException,Exception {
PageContext pageContext = null;
HttpSession session = null;
ServletContext application = null ;
ServletConfig config = null;
JspWriter out = null;
Object page = this;
JspWriter _jspx_out = null;
PageContext _jspx_page_context = null;
try {
response.setContentType ("text/html; charset=gb2312") ;
paqeContext =_jspxFactory.getPageContext(this, request, response,
null, true, 8192, true);
_jspx_page_context = pageContext;
application = paqeContext.getServletContext();
...
    }
}
```
上面Servlet类表明：request、response两个对象是_jspService()方法的形参，当Tomcat调用该方法时会初始化这两个对象。  

而page、pageContext、application、config、session、out都是_jspService()方法的局部变量，由该方法完成初始化。  
  
从上面代码可以发现JSP内置对象的实质：它们要么是jspService()方法的形参，要么是_jspService()方法的局部变量，所以可以直接在JSP脚本（脚本将对应于Servlet的_jspService()方法部分）中调用这些对象，无须创建它们。  
> 提示：  
**由于JSP内置对象都是在_jspService()方法中完成初始化的，因此只能在JSP脚本、JSP输出表达式中使用这些内置对象。千万不要在JSP声明中使用它们！否则，系统将提示找不到这些变量。当编写JSP页面时，一定不要仅停留在JSP页面本身来看问题，这样可能导致许多误解，导致无法理解JSP的运行方式。**  

上面的代码中并没有exception内置对象，这说明：只有当页面的page指令的isErrorPage属性为true时，才可使用exception对象。  
即只有异常处理页面对应Servlet时才会初始化exception对象。  
## JSP主要内置对象有效作用范围比较
- page对象只在同一个JSP页面内有效  

- response对象只在JSP页面（包括当前JSP页面中使用<%@include>标签、<jsp:include>标签和<forward>标签包含的其他JSP页面）内有效  
- request对象在一次访问请求内有效，服务器跳转后依然有效，但客户端跳转之后无效。request表示的是客户端的请求，正常情况下，一次请求服务器只会给予一次回应，那么此时如果服务器端跳转，请求的地址没有改变，也就相当于回应了一次，而如果访问地址改变了，就相当于发出了第二次请求，则第一次请求的内容肯定就已经消失了，所以无法获取  
- session对象在一次回话范围内有效，无论是客户端跳转还是服务器端跳转都有效，但浏览器关闭后则无效  
- application对象在服务器中保存所有用户的共享信息，该对象中保存的信息在整个应用中都有效，使得每个用户都能访问该对象


## 1.application对象
先简单介绍一些Web服务器的实现原理：  
> **虽然常把基于Web应用称为B/S架构的应用，但其实Web应用一样是C/S结构的应用，只是这种应用的服务器是Web服务器，而客户端是浏览器。**  

现在，抛开Web应用直接看Web服务器和浏览器，对于大部分浏览器而言，它通常负责完成三件事情。
- 向远程服务器发送请求
- 读取远程服务器返回的字符串数据
- 负责根据字符串数据渲染一个丰富多彩的页面。

> 提示：  
**实际上，浏览器是一个非常复杂的网络通信程序，它除了可以向服务器发送请求、读取网络数据之外，最大的技术难点在于将HTML文本渲染成页面，建立HTML页面的DOM模型，支持JavaScript脚本程序等。通常浏览器只是对它们进行了简单的包装。**  

Web服务器则负责接收客户端请求，每当接收到客户端连接请求之后，Web服务器应该使用单独的线程为该客户端提供服务：接收请求数据、送回响应数据。  
  
这种架构也称做“请求/响应”架构    
  
对于每次客户端请求而言，Web服务器大致需要完成如下阿济格步骤：
1. 启动单独的线程。
2. 使用I/O流读取用户请求的二进制流数据。
3. 从请求数据中解析参数。
4. 处理用户请求。
5. 生成响应数据。
6. 使用IO流向客户端发送请求数据。

> **最新版的Tomcat已经不需要对每个用户请求都启用单独的线程、使用普通I/O读取用户请求的数据，最新的Tomcat使用的是异步IO，具有更高的性能。**

上面的6个步骤中，第1、2和6步是通用的，可以有Web服务器来完成，但第3、4和5步则存在差异：因为不同请求里包含的请求参数不同，处理用户请求的方式也不同，所生成的响应自然也不同。  
  
实际上，Web服务器会调用Servlet的_jspService()方法来完成第3、4和5步，编写JSP页面时，页面里的静态内容、JSP脚本都会转换成_jspService()方法的执行代码，这些执行代码负责完成解析参数、处理请求、生成响应等业务功能，而Web服务器则负责完成多线程、网络通信等底层功能。
  
Web服务器在执行了第3步解析到用户的请求参数之后，将需要通过这些请求参数来创建HttpServletRequest、HttpServletResponse等对象，作为调用_jspService()方法的参数，实际上一个Web服务器必须为ServletAPI中绝大部分接口提供实现类。    

可以看出，Web应用里的JSP页面、Servlet等程序都将由Web服务器来调用，JSP、Servlet之间通常不会相互调用，这就产生了一个问题：JSP、Servlet之间如何交换数据？  
  
为了解决这个问题，几乎所有Web服务器（包括Java、ASP、PHP、Ruby等）都会提供4个类Map的结构，分别是application、session、request、page，并允许JSP、Servlet将数据放入这4个类Map的结构中，并允许从这4个结构中取出数据。  
这4个Map结构的区别是范围不同。  
- **application**：对于整个Web应用有效，一旦JSP、Servlet将数据放入application中，该数据将可以被该应用下其他所有的JSP、Servlet访问。  
- **session**：仅对一次会话有效，一旦JSP、Servlet将数据放入session中，该数据将可以被本次会话的其他所有的JSP、Servlet访问。  
- **request**：仅对本次请求有效，一旦JSP、Servlet将数据放入request中，该数据将可以被该次请求的其他JSP、Servlet访问。  
- **page**：仅对当前页面有效，一旦JSP、Servlet将数据放入page中，该数据只可以被当前页面的JSP脚本、声明部分访问。  
  
就像显示生活中有两个人，他们的钱需要相互交换，但他们两个人又不能相互接触，那么只能让A把钱存入银行，而B从银行去取钱。因此，可以把application、session、request和page理解为类似银行的角色。  
JSP中的application、session、request和pageContext4个内置对象分别用于操作application、session、request和page范围中的数据。  
  
application对象代表Web应用本身，因此使用application来操作Web应用相关数据。application对象通常有如下两个作用：
- 在整个Web应用的多个JSP、Servlet之间共享数据
- 访问Web应用的配置参数
 
#### 1.让多个JSP、Servlet共享数据
application通过setAttribute(String attrName,Object value)方法将一个值设置成application的attrName属性，该属性的值对整个Web应用有效，因此该Web应用的每个JSP页面或Servlet都可以访问该属性，访问属性的方法为getAttribute(String attrName)。  
  
> 注意：  
**application不仅可以用于两个JSP页面之间共享数据，还可以用于Servlet和JSP之间共享数据。可以把application理解成一个Map对象，任何JSP、Servlet都可以把某个变量放入application中保存，并为之指定一个属性名；而该应用的其他JSP、Servlet就可以根据该属性名来得到这个变量。**  

下面的Servlet代码示范了如何在Servlet中访问application里的变量
```
package lee;

import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.annotation.*;

import java.io.*;

@WebServlet(name="get-application",
	urlPatterns={"/get-application"})
public class GetApplication extends HttpServlet
{
	public void service(HttpServletRequest request,
		HttpServletResponse response)throws IOException
	{
		response.setContentType("text/html;charset=gb2312");
		PrintWriter out = response.getWriter();
		out.println("<html><head><title>");
		out.println("测试application");
		out.println("</title></head><body>");
		
		ServletContext sc = getServletConfig().getServletContext();
		
		out.print("application中当前的counter值为:");
		out.println(sc.getAttribute("counter"));
		out.println("</body></html>");
	}
}

```
由于在Servlet中并没有application内置对象，所以需要通过显示获取该Web应用的ServletContext实例，每个Web应用只有一个ServletContext实例，在JSP页面中可通过application内置对象访问该实例，而Servlet中则必须通过代码获取。  
> 注意：  
该Servlet类同样需要编译成class文件才可使用，实际上该Servlet还使用了@WebServlet注解进行部署。

虽然使用application（即servletContext实例）可以方便多个JSP、Servlet共享数据，但不要仅为了JSP、Servlet共享数据就将数据放入application中。由于application代表整个Web应用，所以通常只应该把Web应用的状态数据放入application里。  

#### 2.获得Web应用配置参数
application还有一个重要的用处：可用于获得Web应用的配置参数。实例：
```
<%
String driver=application.getInitParameter("driver");
String url=application.getInitParameter("url");
String user=application.getInitParameter("user");
String pass=application.getInitParameter("pass");
Class.forName(driver);
Connection conn=DriverManager.getConnection(url,user,pass);
Statement stmt=conn.createStatement();
ResultSet rs=stmt.executeQuery("select * from news_inf");
%>
```
上面的程序使用application的getInitParameter(String paramName)来获取Web应用的配置参数，这些配置参数应该在web.xml文件中使用context-param元素配置，每个<context-param../>元素配置一个参数，该元素有如下两个子元素。
- param-name：配置Web参数名
- param-value：配置Web参数值

web.xml文件中使用<context-param../>元素配置的参数对整个Web应用有效，所以也被称为Web应用的配置参数。与整个Web应用有关的数据，应该通过application对象来操作。  
为了给Web应用配置参数，应在web.xml中增加如下代码：
```
<?xml version="1.0" encoding="GBK"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
	http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
	version="3.1">
	
	<context-param>
		<param-name>driver</param-name>
		<param-value>com.mysql.jdbc.Driver</param-value>
	</context-param>
	
	<context-param>
		<param-name>url</param-name>
		<param-value>jdbc:mysql://localhost:3306/javaee</param-value>
	</context-param>
	
	<context-param>
		<param-name>user</param-name>
		<param-value>root</param-value>
	</context-param>
	
	<context-param>
		<param-name>pass</param-name>
		<param-value>123456</param-value>
	</context-param>

</web-app>
```
> 通过这种方式，可以将一些配置信息放在web.xml文件中配置，避免使用硬编码方式写在代码中，从而更好提高程序的移植性。  


## 2.config对象
config 对象代表当前JSP 配置信息，但JSP 页面通常无须配置，因此也就不存在配置信息。该对象在JSP 页面中非常少用，但在Servlet 则用处相对较大。因为Servlet 需要配置在web.xml 文件中，可以指定配置参数。  

  
```
<!-- 直接输出config的getServletName的值 -->  
<%=config.getServletName()%>  
```
所有的JSP页面都有相同的名字：jsp，所以此行代码将输出jsp。  
实际上，我们也可以在web.xml文件中配置JSP，只是很少用，这样就可以为JSP页面指定配置信息，并且为JSP页面另外设置一个URL。  
  
config对象是ServletConfig的实例，该接口用于获取配置信息的方法是getInitParameter(String paramName)  
下面的程序说明了如何在页面中使用config获取JSP页面的配置信息：  
```
<%@ page language="java" contentType="text/html; charset=gb2132"
    pageEncoding="ISO-8859-1"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>测试config内置对象</title>
</head>
<body>
<!-- 输出该JSP中名为name的参数配置信息 -->
name配置参数的值：<%=config.getInitParameter("name")%><br/>
<!-- 输出该JSP中名为age的参数配置信息 -->
age配置参数的值：<%=config.getInitParameter("age")%>
</body>
</html>
```
配置JSP页面其实也是在web.xml文件中的进行，JSP被当成Servlet配置，为Servlet配置参数使用init-param元素，该元素可以接受param-name和param-value分别指定参数名和参数值。  
在web.xml文件中增加一下代码即可将JSP页面配置在web应用中：  
```
<servlet>  
        <!--指定servlet的名字-->
        <servlet-name>config</servlet-name>  
        <!--指定哪一个JSP页面配置成Servlet-->
        <jsp-file>/configTest2.jsp</jsp-file>
        <!--配置名为name的参数，值为yeeku-->
        <init-param>  
            <param-name>name</param-name>  
            <param-value>yeeku</param-value>  
        </init-param>  
        <!--配置名为age的参数，值为30-->
        <init-param>  
            <param-name>age</param-name>  
            <param-value>30</param-value>  
        </init-param>  
    </servlet>  
<servlet-mapping> 
        <!--指定将config Servlet配置到/config路径--> 
        <servlet-name>config</servlet-name>  
        <url-pattern>/config</url-pattern>  
</servlet-mapping>  
```
上面的配置文件片段中的</init-param>为该Servlet（其实是JSP）配置了两个参数：name和value。上面的配置片段将configTest2.jsp页面配置成名为config的Servlet，并将该Servlet映射到/config处。这就允许我们使用/config来访问该页面。  
  
如果希望JSP页面可以获取web.xml配置文件中的配置信息就必须通过为该JSP页面在web.xml文件中分配的路径来访问页面，因为只有这样访问JSP页面才会让配置文件起作用。  

## 3.exception对象
exception对象是Throwable实例，代表JSP脚本中产生的错误和异常，是JSP页面机制的一部分。在JSP脚本中无须处理异常，即使该异常时checked异常。实际上，JSP脚本所包含的所有可能出现的异常都可以交给错误处理页面进行处理。  
  
异常处理结构：
```
try{    //代码处理段    }   //再普通页面  
  
catch(Exception exception)  
  
{    //异常处理段   }   //在异常处理页面  
```
这似乎典型的异常处理捕捉模块。在JSP页面，在普通的JSP脚本只是执行第一部分即代码处理段；而异常处理页面处理第二个部分即异常处理段。在异常处理段中可以看到一个异常对象，该对象就是内置对象exception。
> **exception对象仅仅在异常处理页面中在有效。**

打开普通页面所生成的Servlet类。将发现以下代码段：
```
public void _jspService(HttpServletRequest request,HttpServletResponse response)  
         throws java.io.IOException, ServletException{  
         try{  
         //所有JSP脚本、静态HTML部分都会转换成此部分代码  
         response.setContentType("text/html;charset = gb2132");  
         ...  
         out.write("</BODY>\r\n");  
         out.write("</HTML>\r\n");  
         }catch(Throwable t){  
         ...  
         //处理该异常  
         if(_jspx_page_context != null) _jspx_page_context.handlePageException(t);  
         }finally{  
         //释放资源  
         _jspxFactory.releasePageContext(_jspx_page_context);  
         }  
}  
```
从上面的代码的第4行可知，JSP脚本和静态HTML部分都将转换成_jspService()方法里的执行性代码——这就是jsp脚本无须处理异常的原因：因为这些脚本已经存在于try块中，一旦try块捕捉到JSP脚本的异常，并且_jspx_page_context不为null，就会由该对象来处理异常，如上面的代码第12行所示。  
  
 _jspx_page_context 对异常的处理也是非常的简单：如果该页面的page指令指定了errorPage属性，将请求foward到errorPage属性指定的页面，否则使用系统指定的页面输出异常信息。  
> 注意：  
**由于只有JSP脚本、输出表达式才会生成_jspx_page_context方法里的代码，所以这两个部分的代码无须处理异常。但是**JSP的声明部分**依然强制处理checked异常，JSP的异常处理机制对JSP声明不起作用。**

当JSP页面page指令的isErrorPage为true时，该页面就会提供exception内置对象。  

## 4.out对象
out对象代表一个页面输出流。通常用于在页面上输出变量已经常量。一般在使用输出表达式的地方，都可以使用out对象来达到同样的效果。  

> 注意：  
**所有使用out的地方，都可使用输出表达式来代替，而且使用输出表达式更加简洁。<%=...%>表达式的本质就是out.print(..);。**


## 5.pageContext对象
这个对象代表页面上下文，该对象主要用于访问JSP之间的共享数据，使用PageContext可以访问page、request、session、appliction范围的变量。  
  
PageContext是PageContext的实例。它提供了如下两个方法来访问page、request、session 、appliction范围的变量。
- **getAttribute(Stringname)**：取得page范围内的name属性。
- **getAttribute(String name, int scope)**：取得指定范围内的name属性。其中scope可以是如下四个值：
    - **PageContext.PAGE_SCOPE**：对应于page范围
    - **PageContext.REQUEST_SCOPE**：对应于request范围
    - **PageContext.SESSION_SCOPE**：对应于session范围
    - **PageContext.APPLICATION_SCOPE**：对应于application范围

与getAttribute（）方法相对应的，也提供了2个对应的setAttribute（）方法。用于将指定变量放入到page、request、session、appliction范围内。  
```
//使用pageContext设置属性，该属性默认在page范围内  
pageContext.setAttribute("page","hello");
//将属性设置在request范围内  
pageContext.setAttribute("request2","hello",pageContext.REQUEST_SCOPE);  
```
可以使用pageContext.getAttributesScope(String paramName)获取各属性所在的范围，其中这些范围获取的都是整型变量，这些整型变量对应如下4个生存范围：
- 1：对应page生存范围
- 2：对应request生存范围
- 3：对应session生存范围
- 4：对应application生存范围

不仅如此，pageContext还可以用于获取其他内置对象，pageContext对象包含如下方法：  
- **ServletRequest getRequest()**:获取request对象
- **ServletRequest getResponse()**：获取response对象
- **ServletConfig getServletConfig()**：获取config对象
- **ServletContext getServletContext()**：获取application对象
- **HttpSession getSession()**：获取session对象
因此一旦在JSP、Servlet编程中获取了pageContext对象，就可以通过它提供的上面方法来获取其他内置对象。  

  
## 6.request对象
request对象是JSP中的重要对象，每个request对象封装着一次用户请求，并且所有的请求参数都被封装在request对象中，因此request对象获取请求参数的重要途径。  
另外request代表本次请求范围，所以还可以用于操作request范围的属性。  

  
### 1.获取请求头/请求参数
web应用是请求/响应架构的应用，浏览器发出请求时通常会附带一些请求头，还可能包含一些请求参数发给服务器，服务端负责解析请求头/请求参数的就是JSP或Servlet，而JSP、Servlet取得请求参数的途径就是request。  
request是HttpServletRequest接口的实例，它提供了如下方法：  
- **String getParanmeter(String paramName)**：获取paramName请求参数的值  
- **Map getParameterMap()**：获取所有请求参数名和参数值所组成的Map对象  
- **Enumeration getParameterMap()**：获取所有请求参数名组成的Enumeration对象  
- **String[] getParameterValues(String name)**：paramName请求参数的值，当请求参数有多个值时，该方法将返回对个值所组成的数组  
HttpServletRequest提供如下方法：
- **String getHeader(String name)**：根据指定请求头的值  
- **java.util.Enumeration<String> getHeaderNames()**: 获得所有请求头的值  
- **java.util.Enumeration<String> getHeaders(String name)**：获得指定请求头的多个值  
- **int getIntHeader(String)**：获取指定请求头的值，并将该值转为整数  

对于开发人员来说，请求头和请求参数都是由用户发送到服务器的数据，区别在于请求头通常由浏览器自动添加，因此一次请求总是包含若干请求头；而请求参数则通常需要开发人员控制添加，让客户端发送请求参数通常分为两种情况。  
- **GET方式的请求**:直接在浏览器地址蓝输入访问地址发送的请求或提交表单发送请求时，该表单对应的form元素没有设置method属性，或设置method属性为get，这几种请求都是GET方式的请求。GET方式的请求会将请求参数名和值转换成字符串，并附加在源URL之后，因此可以在地址栏中看到请求参数的名和值。且GET请求传送的数据量较小，一般不能大于2KB.  
- **POST方式的请求**：这种方式通常使用提交表单（由form HTML元素表示）的方式来发送，且需要设置form元素的mothod属性为post。POST方式传送的数据量较大通常认为POST请求参数的大小不受限制，但往往取决于服务器的限制，POST请求传输的数据量总比GET传输的数据量大。而且POST方式发送的请求参数以及对应的值放在HTML HEADER中传输，用户不能在地址蓝里看到请求参数，安全性相对较高。  
所以，通常使用POST方式发送请求。  
> 提示：  
**并不是每个表单域都会生成请求参数，而是有name属性的表单域才生成请求参数。关于表单域和请求参数的关系遵循如下4点：**
> - **每个有name属性的表单域对应一个请求参数。**
> - **如果有多个表单域有相同的name属性，则多个表单域只能生成一个请求参数，只是该参数有多个值。**
> - **表单域的name属性指定请求参数名，value指定请求参数的值。**
> - **如果表单域设置了disable="disabled"属性，则该表单域不再生成请求参数。**

> **为了获取GET请求里的中文数值，必须借助java.net.URLDecode类。**

### 2.操作request范围的属性
HttpServletRequest还包含如下两个方法，用于设置和获取request范围的属性。
- **setAttribute(String attName,Object attValue)**：将attValue设置成request范围的属性。
- **Object getAttribute(String attName)**：获取request范围的属性。  
  

> **forward用户请求时，请求参数和request范围的属性都不会丢失，即forward后还是原来的请求，并未再次向服务器发送请求。**  

### 3.执行forward或include
request还有一个功能就是执行forward和include，也就是代替JSP所提供的forward和include动作指令。  

HttpServletRequest类提供了一个getRequestDispatcher（String path）方法，其中path就是希望forward或者include的目标路径,该方法返回RequestDispatcher，对象提供了如下两个方法。  
- **forward(ServletRequest request,ServletResponse response)**：执行forward。
- **include(ServletRequest request,Serv
如下代码可将a.jsp页面include到本页面中
```
getRequestDispatcher("/a.jsp").include(request,response);
```
如下代码可将请求forward到a.jsp页面：
```
getRequestDispatcher("/a.jsp").forward(request,response);
```

## 7.response对象
response代表服务器对客户端的响应。大部分时候，程序无须使用response来响应客户端请求，因为有个更简单的响应对象————out，它代表页面输出流，直接使用out响应更简单。  
但out是JspWriter的实例，JspWriter是Writer的子类，Writer是字符流，无法输出非字符内容。例如需要在JSP页面中动态生成一副位图，或者输出一个PDF文档，此时必须使用response作为响应输出。  
  
除此之外，还可以使用response来重定向请求，以及用于向客户端增加cookie。  

### 1.response响应生成非字符响应
response是HttpServletResponse接口的实例，该接口提供了一个getOutputStream()方法，该方法返回响应输出字节流  
```
<%-- 通过contentType属性指定响应数据是图片 --%>
<%@ page contentType="image/png" language="java"%>
<%@ page import="java.awt.image.*,javax.imageio.*,java.io.*,java.awt.*"%>
<%
// 创建BufferedImage对象
BufferedImage image = new BufferedImage(340 , 
	160, BufferedImage.TYPE_INT_RGB);
// 以Image对象获取Graphics对象
Graphics g = image.getGraphics();
// 使用Graphics画图，所画的图像将会出现在image对象中
g.fillRect(0,0,400,400);
// 设置颜色：红
g.setColor(new Color(255,0,0));
// 画出一段弧
g.fillArc(20, 20, 100,100, 30, 120);
// 设置颜色：绿
g.setColor(new Color(0 , 255, 0));
// 画出一段弧
g.fillArc(20, 20, 100,100, 150, 120);
// 设置颜色：蓝
g.setColor(new Color(0 , 0, 255));
// 画出一段弧
g.fillArc(20, 20, 100,100, 270, 120);
// 设置颜色：黑
g.setColor(new Color(0,0,0));
g.setFont(new Font("Arial Black", Font.PLAIN, 16));
// 画出三个字符串
g.drawString("red:climb" , 200 , 60);
g.drawString("green:swim" , 200 , 100);
g.drawString("blue:jump" , 200 , 140);
g.dispose();
// 将图像输出到页面的响应
ImageIO.write(image , "png" , response.getOutputStream());
%>
```
以上页面先设置了服务器响应数据是image/png，这表明服务器响应是一张png图片。接着接着创建了一个BufferedImage对象（代表图像），并获取该BufferedImage的Graphics对象（代表画笔）,然后通过Graphics向BufferedImage中绘制图形，最后一行代码将直接把BufferedImage作为响应发送给客户端。   
  
也可以在其他页面中使用img标签来显示这个图片页面
```
<img src="img.jsp">
```
使用这种临时生成图片的方式就可以非常容易地实现网页上的图形验证码功能，不仅如此，使用response生成非字符响应还可以直接生成PDF文档，Excel文件，这些文件可直接作为报表使用。  

### 2.重定向
重定向是response的另外一个用处，与forward不同的是，重定向会丢失所有的请求参数和request范围的属性。  
因为重定向将生成第二次请求，与前一次请求不在同一个request范围内，所以发送一次请求的请求参数和request范围的属性全部丢失。  
  
HttpServletResponse提供了一个sendRedirect(String path)方法，该方法用于重定向到path资源，即重定向path资源发送请求。  
```
response.sendRedirect("redirect-result.jsp");
```
在地址栏输入http://localhost:8080/jspObject/doRedirect.jsp?name=z2o，然后按回车键，会发现地址栏的URL变成重定向的目标URL，而且原来的请求参数不能访问。  
> 注意：  
**重定向丢失所有的请求参数，使用重定向的效果，与在地址栏里重新输入新地址再按回车键的效果完全一样，即发送了第二次请求。**  

从表面上看，forward动作和redirect动作有些相似：它们都可以将请求传递到另一个页面。但实际上，两者有巨大的差异：
转发（forward）|重定向（redirect）
---|---
执行forward后依然是上一次请求 | 执行redirect后生成第二次请求 
forward的目标页面可以访问原请求的请求参数，因为依然是同一次请求，所有原请求的请求参数，request范围的属性全部存在|redirect的目标页面不能访问原请求的请求参数，因为是第二次请求了，所有原请求的请求参数，request范围的属性全部丢失
执行forward后地址栏里的URL不会改变|执行redirect后地址栏改为重定向的目标URL，相当于在浏览器地址栏里输入新的URL后按回车键


### 3.增加Cookie
cookie通常用于网站记录客户的某些信息，比如客户的用户名及客户的喜好等，一旦用户下次登录，网站可以获取到客户的相关信息，根据这些客户信息，网站可以对客户提供更好的服务。  
  
Cookie与session的不同之处在于：session会随着浏览器的关闭而失效，但Cookie会一直存放在客户端机器上，除非超出了C ookie的生命期限。   
  
增加Cookie也是使用response内置对象完成的，response对象提供如下方法：
- **void addCookie(Cookie cookie)**：增加Cookie。

  
增加Cookie之前，必须先创建Cookie对象。增加Cookie对象的步骤：
1. 创建Cookie实例，Cookie的构造器为Cookie（String name,String value）  
2. 设置Cookie的生命期限，即该Cookie在多长时间内有效  
3. 向客户端写Cookie

request对象提供了getCookies()方法，该方法返回客户端机器上所有Cookie组成的数组，遍历该数组的每个元素，找到希望访问的Cookie即可。  
> **使用Cookie对象必须设置生成期限，否则Cookie将会随浏览器的关闭而自动消失**

  
## 8.session对象
 session也是一个很常用的对象，这个对象代表一起用户会话。一次会话的含义是：从客户端浏览器链接服务器开始，到客户端浏览器与服务器断开为止，这个过程就是一次对话。  
   
session通常用于跟踪用户的会话信息，如判断用户是否登录系统，或者在购物车应用中，用于跟踪用户购买的商品等。   
session范围的属性可以在多个页面之间共享，一旦关闭浏览器，即session结束，session范围内的属性将全部丢失。  
  
session对象是HttpSession的实例，HttpSession有如下两个常用的方法：
- **setAttribute(StringattName,Object attValue)**：设置session范围内attName属性的值为attValue  
- **setAttribute(String attName)**：返回session范围内attName属性的值


> 注意：  
**考虑session本身的目的，通常只应该把与用户会话状态相关的信息放入到session范围内。不要仅仅为了两个页面之间的信息交换，就将该信息放入到session范围内。如果仅仅为了两个页面的交换信息，可以将信息放入到request范围内，然后forward请求即可。  

关于session还有一点需要指出，session机制通常用于保存客户端的状态信息，这些状态信息，需要保存到Web服务器的硬盘上，所以要求session里的属性值必须是可序列化的，否则会引发不可序列化的异常。  

> session的属性值可以是任何可序列化的Java对象。 

# Servlet介绍
JSP的本质就是Servlet，开发者把编写好的JSP页面部署在Web容器中之后，Web容器会将JSP编译成对应的Servlet。  
但直接使用Servlet的坏处是：Servlet的开发效率非常低，特别是当Servlet生成表现层页面时，页面中所有的HTML标签，都需要采用Servlet的输出流来输出，极其繁琐。  
而且Servlet是Java类，必须由程序员开发、修改、维护，这些问题都使Servlet作为表现层技术显得很困难。  
自MVC规范出现后，Servlet的责任开始明确下来，仅仅作为控制器使用，不再需要生成页面标签，也不再作为视图层角色使用。  
  
## 1.Servlet的开发
Servlet通常被称为服务器端小程序，是运行在服务器的程序，用于处理和响应客户端的请求。  
  
    
Servlet是个特殊的Java类，这个Java类必须继承HttpServlet。每个Servlet可以响应客户端的请求。Servlet提供不同的方法用于响应客户端请求。
- **doGet**：用于响应客户端的GET请求
- **doPost**：用于响应客户端的POST请求
- **doPut**：用于响应客户端的PUT请求
- **doDelete**：用于响应客户端的DELETE请求

事实上，客户端的请求通常只有GET和POST两种，Servlet为了响应这两种请求，必须重写doGet()和doPost()方法。  
如果为了响应4个方式的请求，则四个方法都要重写。   
大部分时候，Servlet对于所有请求的响应都是一样的。此时，可以采用重写一个方法来代替上面的几个方法：只需重写service()方法即可响应客户端的所有请求。   
另外，HttpServlet还包含两个方法：
- **init(ServletConfig config)**：创建Servlet实例时，调用该方法的初始化Servlet资源
- **destroy()**：销毁Servlet实例时，自动调用该方法回收资源

通常无须重写init()和destroy()两个方法，除非需要在初始化Servlet时，完成某些资源初始化的方法，才考虑重写init方法。如果需要在销毁Servlet之前，先完成某些资源的回收，比如关闭数据库连接等，才需要重写destroy方法。   

> 注意：  
**不用为Servlet类编写构造器，如需要对Servlet执行初始化操作，应将初始化操作放在Servlet的init()方法中定义。而且，如果重写了init(ServletConfig config)方法，则应在重写该方法的第一行调用super.init(config)。即调用HttpServlet的init方法。**

下面提供一个Servlet示例，该Servlet将获取表单请求参数，并将请求参数显示给客户端。
```
//Servlet必须继承HttpServlet类
@WebServlet(name="firstServlet" 
    , urlPatterns={"/firstServlet"})
public class FirstServlet extends HttpServlet
{
    //客户端的响应方法，使用该方法可以响应客户端所有类型的请求
    public void service(HttpServletRequest request,
        HttpServletResponse response) 
        throws ServletException,java.io.IOException
    {
        //设置解码方式
        request.setCharacterEncoding("GBK");
        response.setContentType("text/html;charSet=GBK");
        //获取name的请求参数值
        String name = request.getParameter("name");
        //获取gender的请求参数值
        String gender = request.getParameter("gender");
        //获取color的请求参数值
        String[] color = request.getParameterValues("color");
        //获取country的请求参数值
        String national = request.getParameter("country"); 
        //获取页面输出流
        PrintStream out = new PrintStream(response.getOutputStream());
        //输出HTML页面标签
        out.println("<html>");
        out.println("<head>");
        out.println("<title>Servlet测试</title>");
        out.println("</head>");
        out.println("<body>");
        //输出请求参数的值：name
        out.println("您的名字：" + name + "<hr/>");
        //输出请求参数的值：gender
        out.println("您的性别：" + gender + "<hr/>");
        //输出请求参数的值：color
        out.println("您喜欢的颜色：");
        for(String c : color)
        {
            out.println(c + " ");
        }
        out.println("<hr/>");
        out.println("您喜欢的颜色：");
        //输出请求参数的值：national
        out.println("您来自的国家：" + national + "<hr/>");
        out.println("</body>");
        out.println("</html>");
    }
}
```
上面的Servlet类继承了HttpServlet类，表明他可以作为一个Servlet使用。其中的service方法用来响应用户请求。该Servlet和之前的九个内置对象那篇中的request1.jsp页面的效果完全相同，都通过HttpServletRequest获取客户端的form请求参数，并显示请求参数的值。  

Servlet和JSP的区别在于:  
- Servlet中没有内置对象，原来JSP中的内置对象都必须由程序显示创建 
- 对于静态的HTML标签，Servlet都必须使用页面输出流逐行输出  

这也是说明，JSP是Servlet的一种简化，使用JSP只需要完成程序员需要输出到客户端的内容，至于JSP脚本如何嵌入一个类中，由JSP容器完成。而Servlet则是个完整的Java类，这个类的service()方法用于生成对客户端的响应。  

普通Servlet类里的service()方法的作用，完全等同于JSP生成Servlet类的_jspService()方法。   
因此原JSP页面的JSP脚本、静态HTML内容，在普通Servlet里都应该转换成service()方法的代码或输出语句；原JSP声明中的内容，对应为在Servlet中定义的成员变量或成员方法。  

## 2.Servlet的配置
编辑好的Servlet 源文件并不能响应用户请求，还必须将其编译成class 文件。将编译后的HelloServlet. class 文件放在WEB-INF/classes 路径下，如果Servlet 有包，则还应该将class 文件放在对应的包路径下。  
为了让Servlet 能响应用户请求，还必须将Servlet 配置在Web 应用中。配置Servlet时，需要修改web.xrnl 文件。  
  
  从Servlet3.0开始，配置Servlet有两种方式。  
  - 在Servlet类中使用@WebServlet注解进行配置
  - 通过在web.xml文件中进行配置
  
使用@WebServlet来配置Servlet时，其有如下常用的属性（都是可选的）：
- **name**：指定该Servlet的名称
- **urlPatterns / value**：这两个属性的作用完全相同，都是指定该Servlet处理的URL
- **asyncSupported**：指定该Servlet是否支持异步操作模式
- **display**：指定该Servlet的显示名
- **initParams**：用于为该Servlet配置参数
- **loadOnStartup**：用于将该Servlet配置成load-on-startup的Servlet

如果打算使用注解来配置Servlet，有两点需要指出：  
- 不要在web.xml文件的根元素(<web-app../>)中指定metadata-complete=“true”  
- 不要在web.xml文件中配置该Servlet

如果打算使用web.xml文件来配置Servlet，则需要配置如下两个部分：
- 配置Servlet的名字：对应web.xml文件中的<servlet/>元素  
- 配置Servlet的URL：对应web.xml文件中的<servlet-mapping/>元素。这一步是可选的，但如果没有为是Servlet配置URL，则该Servlet不能响应用户请求

```
<!-- 配置Servlet的名字 -->  
<servlet>  
    <!-- 指定Servlet的名字， 相当于指定@WebServlet的name属性 -->  
    <servlet-name>firstServlet</servlet-name>  
    <!-- 指定Servlet的实现类 -->  
    <servlet-class>lee.FirstServlet</servlet-class>  
</servlet>  
<!-- 配置Servlet的URL -->  
<servlet-mapping>  
    <!-- 指定Servlet的名字 -->  
    <servlet-name>firstServlet</servlet-name>  
    <!-- 指定Servlet映射的URL地址， 相当于指定@WebServlet的urlPatterns属性 -->  
    <url-pattern>/aa</url-pattern>  
</servlet-mapping>  
```
如果没有在web.xml文件中增加上面的配置片段，那么该Servlet类上的@WebServlet注解就会起作用，该Servlet的URL为注解中的urlPatterns属性。  

## 3.JSP/Servlet生命周期
JSP本质是Servlet，JSP页面将有Web容器编译成对应的Servlet，当Servlet容器中运行时，其实例的创建及销毁等都不是由程序员决定的，而是有Web容器进行控制的。  
  
创建Servlet实例有两个时机：
- 客户端第一次请求某个Servlet时，系统创建该Servlet的实例：大部分的Servlet都是这种Servlet  
 
- Web应用启动时立即创建Servlet实例，即load-on-startup Servlet

每个Servlet的运行都遵循如下生命周期：
1. 创建Servlet 实例。  
 
2. Web 容器调用Servlet 的init 方法，对Servlet 进行初始化。  
 
3. Servlet 初始化后，将一直存在于容器中，用于响应客户端请求。如果客户端有get 请求，容器调用Servlet 的doGet 方法处理并响应请求。对于不同的请求，有不同的处理方法，或者统一使用service 方法处理来响应用户请求。  
 
4. Web 容器角色销毁Servlet 时，调用Servlet 的destroy 方法，通常在关闭Web容器之时销毁Servlet。

## 4.load-on-startup Servlet
这种方式的Servlet在Web应用启动时立即创建Servlet实例，通常是用于某些后台服务的Servlet，或者需要拦截很多请求的Servlet；这种Servlet通常作为应用的基础Servlet使用，提供重要的后台服务。  
  
配置load-on-startup的Servlet有两种方式：
- 在web.xml文件中通过<servlet../>元素的<load-on-startup../>子元素进行配置  
- 通过@WebServlet注解的loadOnStartup属性指定

<load-on-startup../>元素或loadOnStartUp属性都只接收一个整型值，这个整型值越小，Servlet就越优先实例化。  
```
<servlet>  
    <!-- Servlet名 -->  
    <servlet-name>timerServlet</servlet-name>  
    <!-- Servlet的实现类 -->  
    <servlet-class>lee.TimerServlet</servlet-class>  
    <!-- 配置应用启动时，创建Servlet实例 ，相当于指定@WebServlet的loadOnStartup属性 -->  
    <load-on-startup>1</load-on-startup>  
</servlet>  
```

## 5.访问Servlet的配置参数
配置Servlet时，还可以增加额外的配置参数。通过使用配置参数，可以实现提供更好的可移植性，避免将参数以硬编码方式写在程序代码中。  
为Servlet配置参数有两种方式：  
- 通过@WebServlet的initParams属性来指定
- 通过在web.xml文件的<servlet.../>元素中添加<init-param.../>子元素来指定  
第二种方式与为JSP配置初始化参数极为相似，因为JSP的实质就是Servlet，而且配置JSP的实质就是把JSP当Servlet使用。  
  
访问Servlet配置参数通过ServletConfig独享完成，ServletConfig提供getInitParameter()来获取初始化参数，如下  
```
// 获取ServletConfig对象  
ServletConfig config = getServletConfig();  
// 通过ServletConfig对象获取配置参数：dirver  
String driver = config.getInitParameter("driver");  
// 通过ServletConfig对象获取配置参数：url  
String url = config.getInitParameter("url");  
// 通过ServletConfig对象获取配置参数：user  
String user = config.getInitParameter("user");  
// 通过ServletConfig对象获取配置参数：pass  
String pass = config.getInitParameter("pass");  
```
> JSP的内置对象config就是此处的ServletConfig。

ServletConfig获取配置参数的方法和ServletContext获取配置参数的方法完全一样，只是ServletConfig是获得当前Servlet的配置参数，而ServletContext是获取整个Web应用的配置参数。  
  
下面是该Servlet在web.xml文件中的配置片段：
```
<servlet>  
    <!-- 配置Servlet名 -->  
    <servlet-name>testServlet</servlet-name>  
    <!-- 指定Servlet的实现类 -->  
    <servlet-class>lee.TestServlet</servlet-class>  
    <!-- 配置Servlet的初始化参数：driver -->  
    <init-param>  
        <param-name>driver</param-name>  
        <param-value>com.mysql.jdbc.Driver</param-value>  
    </init-param>  
    <!-- 配置Servlet的初始化参数：url -->  
    <init-param>  
        <param-name>url</param-name>  
        <param-value>jdbc:mysql://localhost:3306/javaee</param-value>  
    </init-param>  
    <!-- 配置Servlet的初始化参数：user -->  
    <init-param>  
        <param-name>user</param-name>  
        <param-value>root</param-value>  
    </init-param>  
    <!-- 配置Servlet的初始化参数：pass -->  
    <init-param>  
        <param-name>pass</param-name>  
        <param-value>32147</param-value>  
    </init-param>  
</servlet>  
```
上面配置了4个配置参数，Servlet通过这4个配置参数就可连接数据库。  

  
## 6.使用Servlet作为控制器
使用Servlet作为表现层的工作量太大，所有的HTML标签都需要使用页面输出流生成。  
所以使用Servlet作为表现层有如下三个劣势：
- 开发效率低，所有的HTML标签都需使用页面输出流完成  
- 不利于团队协作开发，美工人员无法参与Servlet界面的开发  
- 程序可维护性差，即使修改一个按钮的标题，都必须重新编辑Java代码，并重新编译

在标准的MVC模式中，Servlet仅作为控制器使用。JavaEE应用框架正式遵循MVC模式的，对于遵循MVC模式的JavaEE应用而言，JSP仅作为表现层(View)技术，其作用有两点：
- 负责收集用户请求参数
- 将应用的处理结果、状态数据呈现给用户
  

Servlet则仅充当控制器(Controller)角色，它的作用类似于调度员：所有用户请求都发送给Servlet，Servlet调用Model来处理用户请求，并调用JSP来呈现处理结果；或者Servlet直接调用JSP将应用的状态数据呈现给用户。  
  
Model通常由JavaBean来充当，所有业务逻辑、数据访问逻辑都在Model中实现。实际上，隐藏在Model下的可能还有很多丰富的组件，例如DAO组件、领域对象等。  
  
控制器负责接收客户端的请求，它既不直接对客户端输出响应，也不处理用户请求，只调用JavaBean来处理用户请求；JavaBean处理结束后，Servlet根据处理结果，调用不同的JSP页面想浏览器呈现处理结果。  
  
下面是各个MVC中各个角色的对应组件：
- **M：Model**，即模型，对应JavaBean
- **V：View**，即视图，对应JSP页面
- **C：Controller**，即控制器，对应Servlet 
  
  
# JSP2的自定义标签
在JSP2中开发标签库的步骤：
1. 开发自定义标签处理类
2. 建立一个\*.tld文件，每个*.tld文件对应一个标签库，每个标签库可包含多个标签
3. 在JSP文件中使用自定义标签  

## 开发自定义标签类
自定义标签类应该继承一个父类：javax.servlet.jsp.tagext.SimpleTagSupport，除此之外，JSP自定义标签类还有如下要求：  
- 如果标签类包含属性，每个属性都有对应的getter和setter方法
- 重写doTag()方法，这个方法负责生成页面内容

```
public class HelloWorldTag extends SimpleTagSupport {  
    // 重写doTag()方法，该方法为标签生成页面内容  
    public void doTag() throws JspException, IOException {  
        // 获取页面输出流，并输出字符串  
        getJspContext().getOut().write("Hello World " + new java.util.Date());  
    }  
```
上面这个标签的处理类非常简答，它继承了SimpleTagSupport父类，并重写了doTage()方法，而doTag方法则负责输出页面内容。该标签没有属性，因此无须提供setter和getter方法。  

## 建立TLD文件
TLD是Tag Library Definition的缩写，即标签库定义，文件的后缀是tld，每个TLD文件对应一个标签库，一个标签库中可包含多个标签。TLD文件也称为标签库定义文件。  
标签库定义文件的根元素是taglib，他可以包含多个tag子元素，每个tag子元素都定义了一个标签。通常可以到Web容器下复制一个标签库定义文件，在其基础上进行修改即可。例如Tomcat中，在webapps\examples\WEB-INF\jsp2路径下包含了一个示例文件。  
  
    
taglib下有如下三个子元素：
- **tlib-version**：指定该标签库实现的版本，这是一个作为标识的内部版本号，对程序没有太大的作用
- **short-name**：该标签库的默认短名，该名称通常也没有太大的用处
- **uri**：这个属性非常重要，它指定该标签库的URI，相当于指定该标签库的唯一标识。JSP页面中使用标签库时就是根据该URI属性来定位标签库的 

  
taglib元素下可以包含多个tag元素，每个tag元素定义一个标签，tag元素下允许出现如下常用子元素：
- **name**：该标签的名称，这个子元素很重要，JSP页面中就是根据该名称来使用此标签的
- **tag-class**：指定标签的处理类
- **body-content**：这个子元素也很重要，它指定标签体内容。其值可以为：
    - tagdependent：指定标签处理类自己负责处理标签体
    - empty：指定该标签只能作用空标签使用
    - scriptless：指定该标签的标签体可以是静态HTML元素、表达式语言，但不允许出现JSP脚步
- **dynamic-attributes**：指定该标签是否支持动态属性。只有当定义动态属性标签时才需要该子元素。

## 使用标签库
在JSP页面中确定指定的标签需要两点：
- 标签库URI：确定使用哪个标签库
- 标签名：确定使用哪个标签
  
使用标签库分成以下两个步骤：
- 导入标签库：使用taglib编译指令导入标签库，就是将标签库和指定前缀关联起来  
- 使用标签：在JSP页面中使用标签

导入标签库的语法：
```
<%@ taglib uri="tagliburi" prefix="tagPrefix" %>
```
其中uri属性指定标签库的URI，这个URI可以确定一个标签库，这个URI就是TLD文件中定义的uri属性对应。而prefix属性指定标签库前缀，即所有使用该前缀的标签将由此标签库处理。  
  
## 带属性的标签
前面的简单标签既没有属性，也没有标签体，用法和功能比较简单。实际上还有如下两种常用的标签：
- 带属性的标签
- 带标签体的标签

带属性标签必须为每个属性提供对应的setter和getter方法。下面是一个带属性标签的示例：
```
public class QueryTag extends SimpleTagSupport {  
    // 定义成员变量来代表标签的属性  
    private String driver;  
    private String url;  
    private String user;  
    private String pass;  
    private String sql;  
  
    // 执行数据库访问的对象  
    private Connection conn = null;  
    private Statement stmt = null;  
    private ResultSet rs = null;  
    private ResultSetMetaData rsmd = null;  
      
    //省略各成员变量的setter和getter方法  
    ...  
  
    public void doTag() throws JspException, IOException {  
        try {  
            // 注册驱动  
            Class.forName(driver);  
            // 获取数据库连接  
            conn = DriverManager.getConnection(url, user, pass);  
            // 创建Statement对象  
            stmt = conn.createStatement();  
            // 执行查询  
            rs = stmt.executeQuery(sql);  
            rsmd = rs.getMetaData();  
            // 获取列数目  
            int columnCount = rsmd.getColumnCount();  
            // 获取页面输出流  
            Writer out = getJspContext().getOut();  
            // 在页面输出表格  
            out.write("<table border='1' bgColor='#9999cc' width='400'>");  
            // 遍历结果集  
            while (rs.next()) {  
                out.write("<tr>");  
                // 逐列输出查询到的数据  
                for (int i = 1; i <= columnCount; i++) {  
                    out.write("<td>");  
                    out.write(rs.getString(i));  
                    out.write("</td>");  
                }  
                out.write("</tr>");  
            }  
        } catch (ClassNotFoundException cnfe) {  
            cnfe.printStackTrace();  
            throw new JspException("自定义标签错误" + cnfe.getMessage());  
        } catch (SQLException ex) {  
            ex.printStackTrace();  
            throw new JspException("自定义标签错误" + ex.getMessage());  
        } finally {  
            // 关闭结果集  
            try {  
                if (rs != null)  
                    rs.close();  
                if (stmt != null)  
                    stmt.close();  
                if (conn != null)  
                    conn.close();  
            } catch (SQLException sqle) {  
                sqle.printStackTrace();  
            }  
        }  
    }  
}  
```
为标签处理类定义成员变量即可代表标签的属性，程序需要为5个属性提供setter和getter方法。  
该标签输出的内容依然由doTage()方法决定。  
  
对于有属性的标签，需要在tld中为<tag../>元素增加<attribute../>子元素，每个<attribute../>定义一个标签属性。<attribute../> 子元素通常还需要指定如下几个子元素：
- **name**：设置属性名，子元素的值是字符串内容
- **required**：设置该属性是否为必须属性，该子元素的值时true或false
- **fragment**：设置该属性是否支持JSP脚本、表达式等动态内容，子元素的值true或false

为配置以上的标签，需要在tld文件中增加如下配置片段：
```
<tag>  
    <!-- 定义标签名 -->  
    <name>query</name>  
    <!-- 定义标签处理类 -->  
    <tag-class>lee.QueryTag</tag-class>  
    <!-- 定义标签体为空 -->  
    <body-content>empty</body-content>  
    <!-- 配置标签属性:driver -->  
    <attribute>  
        <name>driver</name>   
        <required>true</required>  
        <fragment>true</fragment>  
    </attribute>  
    <!-- 配置标签属性:url -->  
    <attribute>  
        <name>url</name>   
        <required>true</required>  
        <fragment>true</fragment>  
    </attribute>  
    <!-- 配置标签属性:user -->  
    <attribute>  
        <name>user</name>   
        <required>true</required>  
        <fragment>true</fragment>  
    </attribute>  
    <!-- 配置标签属性:pass -->  
    <attribute>  
        <name>pass</name>   
        <required>true</required>  
        <fragment>true</fragment>  
    </attribute>  
    <!-- 配置标签属性:sql -->  
    <attribute>  
        <name>sql</name>   
        <required>true</required>  
        <fragment>true</fragment>  
    </attribute>  
</tag>  
```
配置完毕后，就可在页面中使用标签了，先导入标签库，然后使用标签：
```
<%@ taglib uri="http://www.crazyit.org/mytaglib" prefix="mytag"%>  
<!-- 使用标签 ，其中mytag是标签前缀，根据taglib的编译指令，  
    mytag前缀将由http://www.crazyit.org/mytaglib的标签库处理 -->  
......  
<mytag:query  
    driver="com.mysql.jdbc.Driver"  
    url="jdbc:mysql://localhost:3306/javaee"  
    user="root"  
    pass="32147"  
    sql="select * from news_inf"/>  
```
在JSP页面只需使用简单的标签，即可完成复杂的功能。这也正是自定义标签库的目的——以简单的标签，隐藏复杂的逻辑。  
但不推荐在标签处理类中访问数据库，因为标签库是表现层组件，不应该包含任何业务逻辑实现的代码，更不应该执行数据库访问，它只负责显示逻辑。  
  
## 带标签体的标签
带标签体的标签，可以在标签内嵌入其他内容（包括静态的HTML内容和动态的JSP内容），通常用于完成一些逻辑运算。示例如下：  
一样，先定义一个标签处理类：  
```
public class IteratorTag extends SimpleTagSupport{
    //标签属性，用于指定需要被迭代的集合
    private String collection;
    //标签属性，指定迭代集合元素，为集合元素指定的名称
    private String item;
    //省略collection的setter和getter方法
    ...
    //省略item的setter和getter方法
    ...
    //标签的处理方法，标签处理类只需要重写doTag()方法
    public void doTag() throws JspException,IOException{
        //从page scope中获取名为collection的集合
        Collection itemList=(Collection)getJspContext().getAttribute(collection);
        //遍历集合
        for(Object s:itemList){
            //将集合的元素设置到page范围内
            getJspContext().setAttribute(item,s);
            //输出标签体
            getJspBody().invoke(null);
        }
    }
}
```
标签处理类doTag()方法首先从page范围内获取了指定名称的Collection对象，然后遍历Collection对象的元素，每次遍历都调用getJspBody()方法，该方法返回该标签所包含的标签体：JSPFragment对象，执行该对象的invoke()方法，即可输出标签体内容。该标签的作用是：遍历指定集合，每遍历一个集合元素，技术处标签体一次。      
  
因为该标签的标签体不为空，配置该标签时指定body-content为scriptless，该标签的配置代码如下：  
```
<tag>  
    <!-- 定义标签名 -->  
    <name>iterator</name>  
    <!-- 定义标签处理类 -->  
    <tag-class>lee.IteratorTag</tag-class>  
    <!-- 定义标签体不允许出现JSP脚本 -->  
    <body-content>scriptless</body-content>  
    <!-- 配置标签属性:collection -->  
    <attribute>  
        <name>collection</name>   
        <required>true</required>  
        <fragment>true</fragment>  
    </attribute>  
    <!-- 配置标签属性:item -->  
    <attribute>  
        <name>item</name>   
        <required>true</required>  
        <fragment>true</fragment>  
    </attribute>  
</tag>  
```
在JSP页面中使用这个标签，示例如下：
```
<body>  
    <h2>带标签体的标签-迭代器标签</h2>  
    <hr />  
    <%  
        //创建一个List对象  
        List<String> a = new ArrayList<String>();  
        a.add("疯狂Java");  
        a.add("www.crazyit.org");  
        a.add("www.fkit.org");  
        //将List对象放入page范围内  
        pageContext.setAttribute("a", a);  
    %>  
    <table border="1" bgcolor="#aaaadd" width="300">  
        <!-- 使用迭代器标签，对a集合进行迭代 -->  
        <mytag:iterator collection="a" item="item">  
            <tr>  
                <td>${pageScope.item}</td>  
            </tr>  
        </mytag:iterator>  
    </table>  
</body>  
```

> 提示：  
在严格MVC规范下，JSP页面只负责显示数据————而数据通常由控制器（Servlet）放入request范围内，而JSP页面就通过iterator标签迭代输出request范围内的数据。  

## 以页面片段作为属性的标签
JSP2规范的自定义标签还允许直接将一段“页面片段”作为属性，这种方式给自定义标签提供了更大的灵活性。    
  
以“页面片段”为属性的标签与与普通标签区别并不大，只有两个简单的改变：
- 标签处理类中定义类型为JSPFragment的属性，该属性代表了“页面片段”  
- 使用标签库，通过<jsp:attribute.../>动作指令为标签的属性指定值

下面程序定义了一个标签处理类，该标签处理类中定义了一个JspFragment类型的属性，即表明该标签允许使用“页面片段”类型的属性。  
```
public class FragmentTag extends SimpleTagSupport {  
    private JspFragment fragment;  
    //省略fragment的setter和getter方法  
    @Override  
    public void doTag() throws JspException, IOException {  
        JspWriter out = getJspContext().getOut();  
        out.println("<div style='padding:10px;border:1px solid black;" + ";border-radius:20px'>");  
        out.println("<h3>下面是动态传入的JSP片段</h3>");  
        // 调用、输出“页面片段”  
        fragment.invoke(null);  
        out.println("</div");  
    }  
}  
```
上面的程序中定义了fragment成员变量，该成员变量代表了使用该标签时的“页面片段”，配置该标签与配置普通标签并无任何区别，增加如下配置片段即可：
```
<tag>  
    <!-- 定义标签名 -->  
    <name>fragment</name>  
    <!-- 定义标签处理类 -->  
    <tag-class>lee.FragmentTag</tag-class>  
    <!-- 指定该标签不支持标签体 -->  
    <body-content>empty</body-content>  
    <!-- 定义标签属性：fragment -->  
    <attribute>  
        <name>fragment</name>  
        <required>true</required>  
        <fragment>true</fragment>  
    </attribute>  
</tag>  
```
就是一个普通的带属性标签，该标签的标签体为空。  
由于该标签需要一个fragment属性，该属性的类型为JspFragment，因此使用该标签时需要使用<jsp:attribute../>动作指令来设置属性值：  
```
<h2>下面显示的是自定义标签中的内容</h2>
<mytag:fragment>
    <jsp:attribute name="fragment">
    <%-- 使用jsp:attribute标签传入fragment参数（该注释不能放在fragment内） -->  
    <%-- 下面是动态的JSP页面片段 --%>  
    <mytag:helloWorld />  
</jsp:attribute>  
</mytag:fragment>  
<br />  
<mytag:fragment>  
    <jsp:attribute name="fragment">  
    <%-- 下面是动态的JSP页面片段 --%>  
    ${pageContext.request.remoteAddr}  
</jsp:attribute>  
</mytag:fragment>  
```
上面的<jsp:attribute>用于为标签的fragment属性赋值，第一个例子使用了另一个简单标签来生成页面片段；第二个例子使用了JSP2的EL来生成页面片段。

> 提示：  
由于程序指定了fragment标签的标签体为empty，因此程序中fragment开始标签和结束标签之间只能使用<jsp:attribute.../>子元素，不允许出现其他内容，甚至连注释都不允许。  

## 动态属性的标签
前面的标签的属性个数是确定的，绝大部分情况下这种带属性的标签能处理得很好，但在某些特殊情况下，需要传入自定义标签的属性个数是不确定的，属性名也不确定，这就需要借助于动态属性的标签了。  
  
动态属性标签比普通标签多了两个额外需求：
- 标签处理类还需要实现DynamicAttributes接口
- 配置标签时通过<dynamic-attributes.../>子元素指定该标签支持动态属性
```
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

import javax.servlet.jsp.JspException;
import javax.servlet.jsp.JspWriter;
import javax.servlet.jsp.tagext.DynamicAttributes;
import javax.servlet.jsp.tagext.SimpleTagSupport;

/**
 * SimpleTag handler that echoes all its attributes
 */
public class EchoAttributesTag
    extends SimpleTagSupport
    implements DynamicAttributes
{
    private final List<String> keys = new ArrayList<>();
    private final List<Object> values = new ArrayList<>();

    @Override
    public void doTag() throws JspException, IOException {
        JspWriter out = getJspContext().getOut();
        for( int i = 0; i < keys.size(); i++ ) {
            String key = keys.get( i );
            Object value = values.get( i );
            out.println( "<li>" + key + " = " + value + "</li>" );
        }
    }

    @Override
    public void setDynamicAttribute( String uri, String localName,
        Object value )
        throws JspException
    {
        keys.add( localName );
        values.add( value );
    }
}
```
上面的标签处理类实现了DynaAttributesTag接口，就是动态属性标签处理类必须实现的接口，实现该接口必须实现setDynaAttribute()方法，该方法用于为该标签处理类动态地添加属性名和属性值。  
配置该标签时需要额外地指定<dynamic-attribute../>子元素，表明该标签为动态属性标签。  
```
<tag>
    <name>dynaAttr</name>
    <tag-class>DynaAttribtuesTag</tag-class>
    <body-content>empty</body-conten>
    <dynamic-attribute>true</dynamic-attribute>
</tag>
```
使用如下
```
<!-- 导入标签库，指定mytag前缀的标签，  
    由http://www.crazyit.org/mytaglib的标签库处理 -->  
<%@ taglib uri="http://www.crazyit.org/mytaglib" prefix="mytag"%>  
...  
<h2>下面显示的是自定义标签中的内容</h2>  
<h4>指定两个属性</h4>  
<mytag:dynaAttr name="crazyit" url="crazyit.org" />  
<br />  
<h4>指定四个属性</h4>  
<mytag:dynaAttr 书名="疯狂Java讲义" 价格="99.0" 出版时间="2008年" 描述="Java图书" />  
```

# Filter介绍
Filter可认为是Servlet的一种加强版，它主要用于对用户请求进行预处理，也可以对HttpServletResponse进行后处理，是一个典型的处理链。  
使用Filter完整的流程是：Filter对用户请求进行预处理，接着将请求交给Servlet进行处理并生成响应，最后Filter再对服务器响应进行后处理。  

Filter相当于一个中间的盒子，在HttpServletRequest到达Servlet之前，拦截客户端的HttpServletRequest。  
然后，根据需要检查HttpServletRequest，也可以修改HttpServletRequest头和数据。然后传递给Servlet。  
在HttpServletResponse到达客户端之前，拦截HttpServletResponse，根据需要检查，也可以修改HttpServletResponse头和数据。  
  
Filter有如下几个种类：
- 用户授权的Filter：Filter负责检查用户请求，根据请求过滤用户非法请求。  
- 日志Filter：详细记录某些特殊的用户请求。  
- 负责解码的Filter：包括对非标准的用户请求。   
- 能该表XML内容的XSLT Filter等。    
- Filter可负责拦截多个请求或响应；一个请求或响应也可被多个Filter拦截。  

  
创建Filter的两个步骤：
1. 创建Filter处理类
2. web.xml文件中配置Filter

## 创建Filter类
创建Filter必须实现javax.Servlet.Filter接口，在该接口中定义了如下三个方法：  
- **void init(FilterConfig config)**：用于完成Filter的初始化  
- **void destroy()**：用于Filter销毁前，完成某些资源的回收  
- **void doFilter(ServletRequest request , ServletResponse response , FilterChain chain)**：实现过滤功能，该方法就是对每个请求及响应增加的额外处理    

下面是一个日志Filter，这个Filter负责拦截所有的用户请求，并将请求的信息记录在日志中。  
```
@WebFilter(filterName = "log", urlPatterns = { "/*" })  
public class LogFilter implements Filter {  
    // FilterConfig可用于访问Filter的配置信息  
    private FilterConfig config;  
    // 实现初始化方法  
    public void init(FilterConfig config) {  
        this.config = config;  
    }  
    // 实现销毁方法  
    public void destroy() {  
        this.config = null;  
    }  
    // 执行过滤的核心方法  
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)  
            throws IOException, ServletException {  
        // ---------下面代码用于对用户请求执行预处理---------  
        // 获取ServletContext对象，用于记录日志  
        ServletContext context = this.config.getServletContext();  
        long before = System.currentTimeMillis();  
        System.out.println("开始过滤...");  
        // 将请求转换成HttpServletRequest请求  
        HttpServletRequest hrequest = (HttpServletRequest) request;  
        // 输出提示信息  
        System.out.println("Filter已经截获到用户的请求的地址： " + hrequest.getServletPath());  
        // Filter只是链式处理，请求依然放行到目的地址  
        chain.doFilter(request, response);//放行用户请求  
        // ---------下面代码用于对服务器响应执行后处理---------  
        long after = System.currentTimeMillis();  
        // 输出提示信息  
        System.out.println("过滤结束");  
        // 输出提示信息  
        System.out.println("请求被定位到" + hrequest.getRequestURI() + "   所花的时间为: " + (after - before));  
    }  
}  
```
上述代码实现了doFilter()方法，实现了此方法就可以对用户请求进行预处理，也可以实现对服务器响应进行后处理————它们的分界线为是否调用chain.doFilter()，执行该方法之前，即对用户请求进行预处理；执行该方法后，即对服务器响应进行后处理。  
  
这个Filter的作用是拦截所有的请求，然后输出请求地址，然后放行这个请求，然后，输出响应地址，并且输出整个响应过程所需要的时间。这只是个简单的拦截器，我们完全也可以在Filter中根据用户请求的HttpSession，判断用户权限是否足够。如果权限不够，直接调用重定向即可，无须调用chain.doFilter(request, response);方法。  
  
  
## 配置Filter
Filter可以认为是Servlet的“增强版”，因此配置Filter与配置Servlet非常相似，都需要配置如下两个部分：
- 配置Filter名
- 配置Filter拦截URL模式

区别在于：Servlet通常指配置一个URL，而Filter可以同时拦截多个请求的URL。因此，在配置Filter的URL模式时通常会使用**模式字符串**，使得Filter可以拦截多个请求。  
与配置Servlet相似的是，配置Filter同样有两种方式：
- 在Filter类中通过注解进行配置
- 在web.xml文件中通过配置文件进行配置

上面Filter类代码使用@WebFilter配置该Filter的名字为log，它会拦截向/*发送的所有请求。而用web.xml文件配置其作用相同，如下：  
```
<!-- 定义Filter -->  
<filter>  
    <!-- Filter的名字，相当于指定@WebFilter  
        的filterName属性 -->  
    <filter-name>log</filter-name>  
    <!-- Filter的实现类 -->  
    <filter-class>lee.LogFilter</filter-class>   
</filter>  
<!-- 定义Filter拦截的URL地址 -->  
<filter-mapping>  
    <!-- Filter的名字 -->  
    <filter-name>log</filter-name>  
    <!-- Filter负责拦截的URL，相当于指定@WebFilter  
        的urlPatterns属性 -->  
    <url-pattern>/*</url-pattern>  
</filter-mapping>  
```
上面配置Filter时指定url-pattern为/*，即表示该Filter会拦截所有的用户请求。  
  
@WebFilter注解常用属性(都是非必须)：
- **filterName**：指定该Filter的名称  
- **urlPatterns / value**：这两个属性的作用完全相同。都指定该Filter所拦截的URL  
- **initParams**：用于为该Filter配置参数  
- **servletNames**：该属性值指定多个Servlet的名称，用于指定该Filter仅对这几个Servlet执行过滤  
- **displayName**：指定该Filter的显示名  
- **asyncSupported**：指定该Filter是否支持异步操作模式。  
- **dispatcherTypes**：指定该Filter仅对那种dispatcher模式的请求进行过滤。该属性支持ASYNC、ERROR、FORWARD、INCLUDE、REQUEST这5个值的任何组合。默认值为同时过滤5种模式的请求。  
  
实际上，Filter和Servlet非常相似，区别只是Filter的doFilter()方法里多了一个FilterChain的参数，通过该参数可以控制是否放行用户请求。  
在实际项目中，Filter里的doFilter()方法里的代码就是从多个Servlet的service()方法里抽取出通用代码，通过使用Filter可以实现更好的代码复用。  
    
  
由于Filter和Servlet如此相似，所以Filter和Servlet具有完全相同的生命周期行为，且Filter也可以通过<init-param.../>元素或@WebFilter的initParams属性来配置初始化参数，获取Filter的初始化参数则使用FilterConfig的getInitParameter()方法。  
  
下面定义一个较为使用的Filter，该Filter对用户请求进行过滤，Filter将通过doFilter方法来设置request编码的字符集，从而避免每个JSP、Servlet都需要设置；而且还会验证用户是否登录  
```
@WebFilter(filterName = "authority", urlPatterns = { "/*" }, initParams = {  
        @WebInitParam(name = "encoding", value = "GBK"), @WebInitParam(name = "loginPage", value = "/login.jsp") })  
public class AuthorityFilter implements Filter {  
    // FilterConfig可用于访问Filter的配置信息  
    private FilterConfig config;  
    // 实现初始化方法  
    public void init(FilterConfig config) {  
        this.config = config;  
    }  
    // 实现销毁方法  
    public void destroy() {  
        this.config = null;  
    }  
    // 执行过滤的核心方法  
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)  
            throws IOException, ServletException {  
        // 获取该Filter的配置参数  
        String encoding = config.getInitParameter("encoding");  
        String loginPage = config.getInitParameter("loginPage");  
        // 设置request编码用的字符集  
        request.setCharacterEncoding(encoding);   
        HttpServletRequest requ = (HttpServletRequest) request;  
        HttpSession session = requ.getSession(true);  
        // 获取客户请求的页面  
        String requestPath = requ.getServletPath();  
        // 如果session范围的user为null，即表明没有登录  
        // 且用户请求的既不是登录页面，也不是处理登录的页面  
        if (session.getAttribute("user") == null && !requestPath.endsWith(loginPage)) {  
            // forward到登录页面  
            request.setAttribute("tip", "您还没有登录");  
            request.getRequestDispatcher(loginPage).forward(request, response);  
        }  
        // "放行"请求  
        else {  
            chain.doFilter(request, response);  
        }  
    }  
}  
```
通过@WebFilter的initParams属性可以为该Filter配置初始化参数，它可以接受多个@WebInitParam，每个@WebInitParam指定一个初始化参数。  
当然也可以使用web.xml文件中配置该Filter：
```
<!-- 定义Filter -->  
<filter>  
    <!-- Filter的名字 -->  
    <filter-name>authority</filter-name>  
    <!-- Filter的实现类 -->  
    <filter-class>lee.AuthorityFilter</filter-class>  
    <!-- 下面两个init-param元素配置了两个参数 -->  
    <init-param>  
        <param-name>encoding</param-name>  
        <param-value>GBK</param-value>  
    </init-param>  
    <init-param>  
        <param-name>loginPage</param-name>  
        <param-value>/login.jsp</param-value>  
    </init-param>  
</filter>  
<!-- 定义Filter拦截的URL地址 -->  
<filter-mapping>  
    <!-- Filter的名字 -->  
    <filter-name>authority</filter-name>  
    <!-- Filter负责拦截的URL -->  
    <url-pattern>/*</url-pattern>  
</filter-mapping>  
```
实现的效果是，如果没有的登录，则只能访问/login.jsp，反之则可以自由访问其他页面。  

## 使用URL Rewrite实现网站伪静态
对于以JSP为表现层开发的动态网站来说，用户访问的URL通常有如下形式：
```
xx.jsp?param=value...
```
大部分搜索引擎都会优先考虑收录静态的HTML页面，而不是这种动态的\*.jsp、\*.php页面。但实际上绝大多数网站都是动态的，不可能全部是静态的HTML页面，因此互联网上的大部分网站都会考虑使用伪静态——就是\*.jsp、*.php这种动态URL伪装成静态的HTML页面。  
  
对于Java Web应用来说，要实现伪静态非常简单：可以通过Filter拦截所有发向\*.html请求，然后按某种规则将请求forward到实际的*.jsp页面即可。现有的URL Rewrite开源项目为这种思路提供了实现，使用URL Rewrite实现网站伪静态也很简单。  
  
利用URL Rewrite实现网站伪静态：
1. 登录http://www.tuckkey.org/urlrewrite/站点下载URL Rewrite的最新版本  
2. 直接下载URL Rewrite的JAR包，并将该JAR包赋值到Web应用的WEB-INF\lib目录下  
3. 在web.xml文件中配置启用URL Rewrite Filter，在web.xml文件中添加如下配置片段：
```
<filter>  
    <filter-name>UrlRewriteFilter</filter-name>  
    <filter-class>org.tuckey.web.filters.urlrewrite.UrlRewriteFilter</filter-class>  
</filter>  
<filter-mapping>  
    <filter-name>UrlRewriteFilter</filter-name>  
    <url-pattern>/*</url-pattern>  
    <dispatcher>REQUEST</dispatcher>  
    <dispatcher>FORWARD</dispatcher>  
</filter-mapping>  
```
注意：需要在所有的servlet mappings的上面  
4. 在应用的WEB-INF路径下增加urlrewrite.xml文件，该文件定义了伪静态映射规则，这份伪静态规则是基于正则表达式。  
```
<?xml version="1.0" encoding="GBK"?>  
<!DOCTYPE urlrewrite PUBLIC "-//tuckey.org//DTD UrlRewrite 3.2//EN"  
    "http://tuckey.org/res/dtds/urlrewrite3.2.dtd">  
<urlrewrite>  
    <rule>  
        <!-- 所有配置如下正则表达式的请求 -->  
        <from>/userinf-(\w*).html</from>  
        <!-- 将被forward到如下JSP页面，其中$1代表  
            上面第一个正则表达式所匹配的字符串 -->  
        <to type="forward">/userinf.jsp?username=$1</to>  
    </rule>  
</urlrewrite>  
```
上面的规则文件中只定义了一个简单的规则：所有发向/userinf-(\w\*).html的请求都将被forward到userinf.jsp，并将(\w*)正则表达式所匹配的内容作为username参数值。根据这个伪静态规则，需要为该应用提供了一个userinf.jsp页面，该页面只是一个模拟了一个显示用户信息的页面，页面代码如下：  
```
<%@ page contentType="text/html; charset=GBK" language="java"  
    errorPage=""%>  
<%  
    // 获取请求参数  
    String user = request.getParameter("username");  
%>  
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"  
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">  
<html xmlns="http://www.w3.org/1999/xhtml">  
<head>  
<title><%=user%>的个人信息</title>  
<meta name="website" content="http://www.crazyit.org" />  
</head>  
<body>  
    <%  
        // 此处应该通过数据库读取该用户对应的信息  
        // 此处只是模拟，因此简单输出：  
        out.println("现在时间是：" + new java.util.Date() + "<br/>");  
        out.println("用户名：" + user);  
    %>  
</body>  
</html>  
```
地址栏仍然会显示为html结尾的地址
    
      
# Listener介绍
当Web应用在Web容器中运行时，Web应用内部会不断地发生各种事件：如Web应用被启动、Web应用被停止，用户session开始、用户session结束、用户请求到达等，通常来说，这些Web事件对Web开发者是透明的。  
  
Servlet API提供了大量监听器来监听Web项目的内部事件，从而允许当Web内部事件发生时回调事件监听器内的方法。  
使用Listener只需要两个步骤：
- 定义Listener实现类
- 通过注解或web.xml文件中配置Listener

## 实现Listener类
常用的Web事件监听器接口有如下几个：
- **ServletContextListener**：用于监听Web应用的启动和关闭  
- **ServletContextAttributeListener**：用于监听ServletContext范围（application）内属性的改变  
- **ServletRequestListener**：用于监听用户请求  
- **ServletRequestAttributeListener**：用于监听ServletRequest范围（request）内属性的改变  
- **HttpSessionListener**：用于监听用户session的开始和结束  
- **HttpSessionAttributeListener**：用于监听HttpSession范围（session）内属性的改变

从上面可以看出，ServletContextListener的作用有点类似load-on-startup Servlet，都可以用于在Web应用启动时，回调方法来启动某些后台程序，这些后台程序负责为系统运行提供支持。  
  
  
下面创建一个获取数据库连接的Listener，该Listener会在应用启动时获取数据库连接，并将获得的连接设置为application范围内的属性：
```
@WebListener  
public class GetConnListener implements ServletContextListener {  
    // 应该启动时，该方法被调用。  
    public void contextInitialized(ServletContextEvent sce) {  
        try {  
            // 取得该应用的ServletContext实例  
            ServletContext application = sce.getServletContext();  
            // 从配置参数中获取驱动  
            String driver = application.getInitParameter("driver");  
            // 从配置参数中获取数据库url  
            String url = application.getInitParameter("url");  
            // 从配置参数中获取用户名  
            String user = application.getInitParameter("user");  
            // 从配置参数中获取密码  
            String pass = application.getInitParameter("pass");  
            // 注册驱动  
            Class.forName(driver);  
            // 获取数据库连接  
            Connection conn = DriverManager.getConnection(url, user, pass);  
            // 将数据库连接设置成application范围内的属性  
            application.setAttribute("conn", conn);  
        } catch (Exception ex) {  
            System.out.println("Listener中获取数据库连接出现异常" + ex.getMessage());  
        }  
    }  
  
    // 应该关闭时，该方法被调用。  
    public void contextDestroyed(ServletContextEvent sce) {  
        // 取得该应用的ServletContext实例  
        ServletContext application = sce.getServletContext();  
        Connection conn = (Connection) application.getAttribute("conn");  
        // 关闭数据库连接  
        if (conn != null) {  
            try {  
                conn.close();  
            } catch (SQLException ex) {  
                ex.printStackTrace();  
            }  
        }  
    }  
}  
```
上面代码重写了ServletContextListener的contextInitialized()、contextDestroyed()方法，这两个方法分别在应用启动、关闭时被触发。  
  
> **在本例中的ServletContextListener把一个数据库连接（Connection实例）设置成application属性，这样将导致所有页面都使用相同的Connection实例，实际上这种做法的性能非常差。
实用的做法是：应用启动时将一个数据源（jaavx.sql.DataSource实例）设置成application属性，而所有JSP页面都通过DataSource实例来取得数据库连接，再进行数据库访问，这样就好多了。**
  
ServletContextListener获取的是Web应用的配置参数，而不是像Servlet和Filter获取本身的配置参数。这是因为配置Listener时十分简单，只要简单地指定Listener实现类即可，不能配置初始化参数。  
  
## 配置Listener
配置Listener只要向Web应用注册Listener实现类即可，无须配置参数之类的东西，因此十分简单。为Web应用配置Listener也有两种方式：
- 使用@WebListener修饰Listener实现类即可
- 在web.xml文档中使用<listener../>元素进行配置
  
使用@WebListener时通常无须指定任何属性，只要使用该注解修饰Listener实现类即可向Web应用注册该监听器。  
在web.xml中使用<listener../>元素进行配置时只要配置如下子元素即可。
- listener-class：指定Listener实现类

  
## 使用ServletContextAttributeListener
ServletContextAttributeListener用于监听ServletContext（application）范围内属性的变化，实现该接口的监听器需要实现如下三个方法：  
- **attributeAdded(ServletContextAttributeEvent event)**：当程序把一个属性存入application范围时触发该方法  
- **attributeRemoved(ServletContextAttributeEvent event)**：当程序把一个属性从application范围删除时触发该方法  
- **attributeReplaced(ServletContextAttributeEvent event)**：当程序替换application范围内的属性时将触发该方法

  
```
@WebListener  
public class MyServletContextAttributeListener implements ServletContextAttributeListener {  
    // 当程序向application范围添加属性时触发该方法  
    public void attributeAdded(ServletContextAttributeEvent event) {  
        ServletContext application = event.getServletContext();  
        // 获取添加的属性名和属性值  
        String name = event.getName();  
        Object value = event.getValue();  
        System.out.println(application + "范围内添加了名为" + name + "，值为" + value + "的属性!");  
    }  
  
    // 当程序从application范围删除属性时触发该方法  
    public void attributeRemoved(ServletContextAttributeEvent event) {  
        ServletContext application = event.getServletContext();  
        // 获取被删除的属性名和属性值  
        String name = event.getName();  
        Object value = event.getValue();  
        System.out.println(application + "范围内名为" + name + "，值为" + value + "的属性被删除了!");  
    }  
  
    // 当application范围的属性被替换时触发该方法  
    public void attributeReplaced(ServletContextAttributeEvent event) {  
        ServletContext application = event.getServletContext();  
        // 获取被替换的属性名和属性值  
        String name = event.getName();  
        Object value = event.getValue();  
        System.out.println(application + "范围内名为" + name + "，值为" + value + "的属性被替换了!");  
    }  
}  
```
  
    
      
## 使用ServletRequestListener和ServletRequestAttributeListener
ServletRequestListener用于监听用户请求的到达，实现该接口的监听器需要实现如下两个方法：
- **requestInitialized(ServletRequestEvent sre)**：用户请求到达、被初始化时触发该方法  
- **requestDestroyed(ServletRequestEvent sre)**：用户请求结束、被销毁时触发该方法

ServletRequestAttributeListener则用于监听ServletRequest(request)范围内属性的变化，实现该接口的监听器需要实现attributeAdded()、attributeRemoved()、attributeReplaced()三个方法。   
  
  
ServletRequestAttributeListener与ServletContextAttributeListener的作用相似，都用于监听属性的改变，只是ServletRequestAttributeListener监听request范围内属性的改变。    
可以采用一个监听器类来监听多种事件，只要这个监听器类实现多个监听器接口即可  
```
@WebListener  
public class RequestListener implements ServletRequestListener, ServletRequestAttributeListener {  
    // 当用户请求到达、被初始化时触发该方法  
    public void requestInitialized(ServletRequestEvent sre) {  
        HttpServletRequest request = (HttpServletRequest) sre.getServletRequest();  
        System.out.println("----发向" + request.getRequestURI() + "请求被初始化----");  
    }  
  
    // 当用户请求结束、被销毁时触发该方法  
    public void requestDestroyed(ServletRequestEvent sre) {  
        HttpServletRequest request = (HttpServletRequest) sre.getServletRequest();  
        System.out.println("----发向" + request.getRequestURI() + "请求被销毁----");  
    }  
  
    // 当程序向request范围添加属性时触发该方法  
    public void attributeAdded(ServletRequestAttributeEvent event) {  
        ServletRequest request = event.getServletRequest();  
        // 获取添加的属性名和属性值  
        String name = event.getName();  
        Object value = event.getValue();  
        System.out.println(request + "范围内添加了名为" + name + "，值为" + value + "的属性!");  
    }  
  
    // 当程序从request范围删除属性时触发该方法  
    public void attributeRemoved(ServletRequestAttributeEvent event) {  
        ServletRequest request = event.getServletRequest();  
        // 获取被删除的属性名和属性值  
        String name = event.getName();  
        Object value = event.getValue();  
        System.out.println(request + "范围内名为" + name + "，值为" + value + "的属性被删除了!");  
    }  
  
    // 当request范围的属性被替换时触发该方法  
    public void attributeReplaced(ServletRequestAttributeEvent event) {  
        ServletRequest request = event.getServletRequest();  
        // 获取被替换的属性名和属性值  
        String name = event.getName();  
        Object value = event.getValue();  
        System.out.println(request + "范围内名为" + name + "，值为" + value + "的属性被替换了!");  
    }  
}  
```
  
    
      
## 使用HttpSessionListener 和HttpSessionAttributeListener 
HttpSessionListener 用于监听用户session的创建和销毁，实现该接口的监听器需要实现如下两个方法：
- sessionCreated(HttpSessionEvent se)：用户与服务器的会话开始、创建时触发该方法  
- sessionDestroyed(HttpSessionEvent se)：用户与服务器的会话断开、销毁时触发该方法
  
  
HttpSessionAttributeListener则用于监听HttpSession（session）范围内属性的变化，实现该接口的监听器需要实现attributeAdded()、attributeRemoved()、attributeReplacd()三个方法。  
由此可见，HttpSessionAttributeListener与ServletContextAttributeListener的作用相似，都用于监听属性的改变。  
只是HttpSessionAttributeListener监听session范围内属性的改变，而ServletContextAttributeListener监听的是application范围内属性的改变。  
实现HttpSessionListener接口的监听器可以监听每个用户会话的开始和断开，因此可以通过该监听器来监听系统的在线用户。  
```
@WebListener  
public class OnlineListener implements HttpSessionListener {  
    // 当用户与服务器之间开始session时触发该方法  
    public void sessionCreated(HttpSessionEvent se) {  
        HttpSession session = se.getSession();  
        ServletContext application = session.getServletContext();  
        // 获取session ID  
        String sessionId = session.getId();  
        // 如果是一次新的会话  
        if (session.isNew()) {  
            String user = (String) session.getAttribute("user");  
            // 未登录用户当游客处理  
            user = (user == null) ? "游客" : user;  
            Map<String, String> online = (Map<String, String>) application.getAttribute("online");  
            if (online == null) {  
                online = new Hashtable<String, String>();  
            }  
            // 将用户在线信息放入Map中  
            online.put(sessionId, user);  
            application.setAttribute("online", online);  
        }  
    }  
  
    // 当用户与服务器之间session断开时触发该方法  
    public void sessionDestroyed(HttpSessionEvent se) {  
        HttpSession session = se.getSession();  
        ServletContext application = session.getServletContext();  
        String sessionId = session.getId();  
        Map<String, String> online = (Map<String, String>) application.getAttribute("online");  
        if (online != null) {  
            // 删除该用户的在线信息  
            online.remove(sessionId);  
        }  
        application.setAttribute("online", online);  
    }  
}  
```
application范围内的Map就记录了当前应用的所有在线用户  

显示在线用户的界面：
```
<%@ page contentType="text/html; charset=GBK" language="java" errorPage="" %>
<%@ page import="java.util.*" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
	"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
	<title> 用户在线信息 </title>
	<meta name="website" content="http://www.crazyit.org" />
</head>
<body>
在线用户：
<table width="400" border="1">
<%
Map<String , String> online = (Map<String , String>)application
	.getAttribute("online");
for (String sessionId : online.keySet())
{%>
<tr>
	<td><%=sessionId%>
	<td><%=online.get(sessionId)%>
</tr>
<%}%>
</body>
</html>
```

# JSP2特性
JSP2主要增加了如下新特性：
- 直接配置JSP属性
- 表达式语言
- 简化的自定义标签API
- Tag文件语法

如果需要使用JSP2语法，其web.xml文件必须使用Servlet2.4以上版本的配置文件。Servlet2.4配置文件的根元素写法如下
```
<web-app xmlns="http://java.sun.com/xml/ns/j2ee"    
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"    
          xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee    
          http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"    
          version="2.4">   
</web-app>   
```
  
## 配置JSP属性
JSP属性定义使用<jsp-property-group/>元素配置，主要包括如下4个方面：
- 是否允许使用表达式语言：使用<el-ignored/>元素确定，默认值为false，即允许使用表达式语言  
- 是否允许使用JSP脚本：使用<scripting-invalid/>元素确定，默认值为false，即允许使用JSP脚本  
- 声明JSP页面的编码：使用<page-encoding/>元素确定，配置该元素后，可以代替每个页面里page指令contentType属性的charset部分  
- 使用隐士包含：使用<include-prelude/>和<include-coda/>元素确定，可以代替在每个页面里使用include编译指令来包含其他页面
> 此处隐式包含的作用与JSP提供的静态包含的作用相似
  
下面web.xml文件配置了该应用下的系列属性：
```
<?xml version="1.0" encoding="GBK"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
	http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
	version="3.1">
	<!-- 关于JSP的配置信息 -->
	<jsp-config>
		<jsp-property-group>
			<!-- 对哪些文件应用配置 -->
			<url-pattern>/noscript/*</url-pattern>
			<!-- 忽略表达式语言 -->
			<el-ignored>true</el-ignored>
			<!-- 页面编码的字符集 -->
			<page-encoding>GBK</page-encoding>
			<!-- 不允许使用Java脚本 -->
			<scripting-invalid>true</scripting-invalid>
			<!-- 隐式导入页面头  -->
			<include-prelude>/inc/top.jspf</include-prelude>
			<!-- 隐式导入页面尾 -->
			<include-coda>/inc/bottom.jspf</include-coda>
		</jsp-property-group>
		<jsp-property-group>
			<!-- 对哪些文件应用配置 -->
			<url-pattern>*.jsp</url-pattern>
			<el-ignored>false</el-ignored>
			<!-- 页面编码字符集 -->
			<page-encoding>GBK</page-encoding>
			<!-- 允许使用Java脚本 -->
			<scripting-invalid>false</scripting-invalid>
		</jsp-property-group>
		<jsp-property-group>
			<!-- 对哪些文件应用配置 -->
			<url-pattern>/inc/*</url-pattern>
			<el-ignored>false</el-ignored>
			<!-- 页面编码字符集 -->
			<page-encoding>GBK</page-encoding>
			<!-- 不允许使用Java脚本 -->
			<scripting-invalid>true</scripting-invalid>
		</jsp-property-group>
	</jsp-config>

	<context-param>
		<param-name>author</param-name>
		<param-value>yeeku</param-value>
	</context-param>

</web-app>
```
上面的配置文件中配置了三个<jsp-property-group.../>元素，每个元素配置了一组JSP属性，用于指定哪些JSP页面应该满足这样的规则。  
> 如果在不允许使用JSP脚本的页面中使用JSP脚本，则该页面将出现错误。

  
## 表达式语言
表达式语言是一种简化的数据访问方式。使用表达式语言可以方便地访问JSP的隐含对象和JavaBean组件。  
在JSP规范中，建议尽量使用表达式语言使JSP文件的格式一致，避免使用JSP脚本。  
> 表达式语言仅仅是一种数据访问语言，可以方便地访问应用程序数据，避免使用JSP脚本

表达式语法：
```
${expression}
```
  
### 1.表达式语言支持的算数运算符和逻辑运算符
表达式语言支持加、减、乘、除、求余等算数运算符，还支持div、mod等运算符，即除法和求余。  
而且表达式语言把所有数值都当做浮点数处理，例如3/4等于3.0/4.0得到的结果是0.75、3/0等于3.0/0.0，结果为Infinity。  
  
    
逻辑运算可以在数字与数字之间比较，还可以在字符与字符之间比较，字符串的比较是根据其对应Unicode值来比较大小的。  
表达式语言 | 表达式语言 |含义
-- | -- | --
\> | gt | greater than 
\< | lt | less than
== | eq | equal
!= | ne | not equal
>= | ge | greater equal
<= | le | less equal


### 2.表达式语言的内置对象
使用表达式语言可以直接获取请求参数值，可以获取页面中JavaBean的指定属性值，获取请求头及获取page、request、session和application范围的属性值等。  
表达式语言包含如下11个内置对象：
- pageContext：代表该页面的pageContext对象，与JSP的pageContext内置对象相同  
- pageScope：用于获取page范围的属性值  
- requestScope：用于获取request范围的属性值  
- sessionScope：用于获取session范围的属性值
- applicationScope：用于获取application范围的属性值  
- param：用于获取请求的参数值  
- paramValues：用于获取请求的参数值，与param的区别在于，该对象用于获取属性值为数组的属性值  
- header：用于获取请求头的属性  
- headerValues：用于获取请求头的属性，与header的区别在于，该对象用于获取属性值为数组的属性值  
- initParam：用于获取请求Web应用的初始化参数  
- cookie：用于获取指定的Cookie值  

```
<%--
网站: <a href="http://www.crazyit.org">疯狂Java联盟</a>
author  yeeku.H.lee kongyeeku@163.com
version  1.0
Copyright (C), 2001-2016, yeeku.H.Lee
This program is protected by copyright laws.
Program Name:
Date: 
--%>

<%@ page contentType="text/html; charset=GBK" language="java" errorPage="" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
	"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
	<title> 表达式语言 - 内置对象 </title>
	<meta name="website" content="http://www.crazyit.org" />
</head>
<body>
	<h2>表达式语言 - 内置对象</h2>
	请输入你的名字：
	<!-- 通过表单提交请求参数 -->
	<form action="implicit-objects.jsp" method="post">
		<!-- 通过${param['name']} 获取请求参数 -->
		你的名字 = <input type="text" name="name" value="${param['name']}"/>
		<input type="submit" value='提交'/>
	</form><br/>
	<% session.setAttribute("user" , "abc");
	// 下面三行代码添加Cookie
	Cookie c = new Cookie("name" , "yeeku");
	c.setMaxAge(24 * 3600);
	response.addCookie(c);
	%>
	<table border="1" width="660" bgcolor="#aaaadd">
		<tr>
			<td width="170"><b>功能</b></td>
			<td width="200"><b>表达式语言</b></td>
			<td width="300"><b>计算结果</b></td>
		<tr>
			<!-- 使用两种方式获取请求参数值 -->
			<td>取得请求参数值</td>
			<td>\${param.name}</td>
			<td>${param.name}&nbsp;</td>
		</tr>
		<tr>
			<td>取得请求参数值</td>
			<td>\${param["name"]}</td>
			<td>${param["name"]}&nbsp;</td>
		</tr>
		<tr>
			<!-- 使用两种方式获取指定请求头信息 -->
			<td>取得请求头的值</td>
			<td>\${header.host}</td>
			<td>${header.host}</td>
		</tr>
		<tr>
			<td>取得请求头的值</td>
			<td>\${header["accept"]}</td>
			<td>${header["accept"]}</td>
		</tr>
		<!-- 获取Web应用的初始化参数值 -->
		<tr>
			<td>取得初始化参数值</td>
			<td>\${initParam["author"]}</td>
			<td>${initParam["author"]}</td>
		</tr>
		<!-- 获取session返回的属性值 -->
		<tr>
			<td>取得session的属性值</td>
			<td>\${sessionScope["user"]}</td>
			<td>${sessionScope["user"]}</td>
		</tr>
		<!-- 获取指定Cookie的值 -->
		<tr>
			<td>取得指定Cookie的值</td>
			<td>\${cookie["name"].value}</td>
			<td>${cookie["name"].value}</td>
		</tr>
	</table>
</body>
</html>
```
两种方式获取请求参数值：
```
${param.name}
```
```
${param["name"]}
```
  
### 3.表达式语言的自定义函数
自定义函数的开发步骤非常类似于标签的开发步骤，定义方式也几乎一样。区别在于自定义标签直接在页面上生成输出，而自定义函数则需要在表达式语言中使用。  
> **函数功能大大扩充了EL的功能，EL本身只是一种数据访问语言，因此它不支持调用方法。如果需要在EL中进行更复杂的处理，就可以通过函数来完成。
函数的本质是：提供一种语法允许在EL中调用某个类的静态方法。**

自定义函数的开发步骤：
1. 开发函数处理类：函数处理类就是普通类，这个普通类中包含若干个静态方法，每个静态方法都可定义一个函数。
```
public class Functions
{
	// 对字符串进行反转
	public static String reverse( String text )
	{
		return new StringBuffer( text ).reverse().toString();
	}
	// 统计字符串的个数
	public static int countChar( String text )
	{
		return text.length();
	}
}

```
> 可以直接使用JDK或其他项目提供的类，只要这个类包含静态方法即可

2. 使用标签库定义函数：定义函数的方法与定义标签的方法大致相似。在<tagli.../>元素下增加<tag.../>元素用于定义自定义标签；增加<function.../>元素则用于定义自定义函数。每个<function.../>元素只要有三个子元素即可：
- name：指定自定义函数的函数名
- function-class：指定自定义函数的处理类
- function-signature：指定自定义函数对应的方法

下面的标签库定义（TLD）文件将上面的Functions.java类中所包含的两个方法定义成两个函数
```
<?xml version="1.0" encoding="GBK"?>
<taglib xmlns="http://java.sun.com/xml/ns/j2ee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee 
	web-jsptaglibrary_2_0.xsd" version="2.0">
	<tlib-version>1.0</tlib-version>
	<short-name>crazyit</short-name>
	<!-- 定义该标签库的URI -->
	<uri>http://www.crazyit.org/tags</uri>
	<!-- 定义第一个函数 -->
	<function>
		<!-- 定义函数名:reverse -->
		<name>reverse</name>
		<!-- 定义函数的处理类 -->
		<function-class>lee.Functions</function-class>
		<!-- 定义函数的实现方法-->
		<function-signature>
			java.lang.String reverse(java.lang.String)</function-signature>
	</function>
	<!-- 定义第二个函数: countChar -->
	<function>
		<!-- 定义函数名:countChar -->
		<name>countChar</name>
		<!-- 定义函数的处理类 -->
		<function-class>lee.Functions</function-class>
		<!-- 定义函数的实现方法-->
		<function-signature>int countChar(java.lang.String)
			</function-signature>
	</function>
</taglib>
```
3. 在JSP页面的EL中使用函数：一样需要先导入标签库，然后再使用函数。
```
<%@ page contentType="text/html; charset=GBK" language="java" errorPage="" %>
<%@ taglib prefix="crazyit" uri="http://www.crazyit.org/tags"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
	"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
	<title> new document </title>
	<meta name="website" content="http://www.crazyit.org" />
</head>
<body>
	<h2>表达式语言 - 自定义函数</h2><hr/>
	请输入一个字符串：
	<form action="useFunctions.jsp" method="post">
		字符串 = <input type="text" name="name" value="${param['name']}">
		<input type="submit"  value="提交">
	</form>
	<table border="1" bgcolor="aaaadd">
		<tr>
		<td><b>表达式语言</b></td>
		<td><b>计算结果</b></td>
		<tr>
		<tr>
			<td>\${param["name"]}</td>
			<td>${param["name"]}&nbsp;</td>
		</tr>
		<!--  使用reverse函数-->
		<tr>
			<td>\${crazyit:reverse(param["name"])}</td>
			<td>${crazyit:reverse(param["name"])}&nbsp;</td>
		</tr>
		<tr>
			<td>\${crazyit:reverse(crazyit:reverse(param["name"]))}</td>
			<td>${crazyit:reverse(crazyit:reverse(param["name"]))}&nbsp;</td>
		</tr>
		<!-- 使用countChar函数 -->
		<tr>
			<td>\${crazyit:countChar(param["name"])}</td>
			<td>${crazyit:countChar(param["name"])}&nbsp;</td>
		</tr>
	</table>
</body>
</html>
```

> 通过上面可以发现，自定义函数的实质：就是将指定Java类的静态方法暴露或可以在EL中使用的函数，所以可以定义成函数的方法必须用public static修饰

## TagFile支持
TagFile是自定义标签的简化用法，使用TagFile可以无须定义标签处理类和标签库文件，但仍然可以在JSP页面中使用自定义标签。  
下面以TagFile建立一个迭代器标签，步骤如下：
1. 建立Tag文件，在JSP所支持的Tag File规范下，Tag File处理了标签处理类，它的格式类似于JSP文件。可以这样理解：**如同JSP可以代替Servlet作为表现层一样，Tag File则可以代替标签处理类。**
Tag File具有如下5个编译指令：
- taglib：作用与JSP文件中的taglib指令效果相同，用于导入其他标签库  
- include：作用与JSP文件中的include指令效果相同，用于导入其他JSP或静态页面  
- tag：作用类似于JSP文件中的page指令，有pageEncoding、body-content等属性，用于设置页面编码等属性  
- attribute：用于设置自定义标签的属性，类似于自定义标签处理类中的标签属性  
- variable：用于设置自定义标签的变量，这些变量将传给JSP页面使用  

```
<%@ tag pageEncoding="GBK" import="java.util.List"%>
<!-- 定义了四个标签属性 -->
<%@ attribute name="bgColor" %>
<%@ attribute name="cellColor" %>
<%@ attribute name="title" %>
<%@ attribute name="bean" %>
<table border="1" bgcolor="${bgColor}">
<tr>
	<td><b>${title}</b></td>
</tr>
<%List<String> list = (List<String>)
	request.getAttribute("a");
// 遍历输出list集合的元素
for (Object ele : list){%>
	<tr>
		<td bgcolor="${cellColor}">
		<%=ele%>
		</td>
	</tr>
<%}%>
</table>
```
Tag File的命名必须遵守如下规则：tagName.tag。后缀名必须是tag。将该文件保存在Web应用下的某个路径下，这个路径相当于标签库的URI名。
2. 在页面中使用自定义标签时，需要先导入标签库，再使用标签。不过与导入标签存在一些差异。由于此时的标签库没有URI，只有标签库路径，因此导入标签时，使用如下语法：
```
<%@ taglib prefix="tagPrefix" tagdir="path" %>
```
下面是JSP页面
```
<%@ page contentType="text/html; charset=GBK" language="java" errorPage="" %>
<%@ page import="java.util.*" %>
<%@ taglib prefix="tags" tagdir="/WEB-INF/tags" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
	"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
	<title>迭代器tag file</title>
	<meta name="website" content="http://www.crazyit.org" />
</head>
<body>
	<h2>迭代器tag file</h2>
	<%
	// 创建集合对象，用于测试Tag File所定义的标签
	List<String> a = new ArrayList<String>();
	a.add("疯狂Java讲义");
	a.add("轻量级Java EE企业应用实战");
	a.add("疯狂Ajax讲义");
	// 将集合对象放入页面范围
	request.setAttribute("a" , a);
	%>
	<h3>使用自定义标签</h3>
	<tags:iterator bgColor="#99dd99" cellColor="#9999cc"
		title="迭代器标签" bean="a" />
</body>
</html>
```
使用如下代码来使用标签：
```
<tags:iterator .../>
```
Tag File是自定义标签的简化。事实上，就如同JSP文件会编译成Servlet一样，Tag File也会编译成标签处理类，自定义标签的后台依然由标签处理类完成，而这个过程由容器完成。 

# Servlet3.0新特性
## Servlet3.0的注解
抛弃了采用web.xml配置Servlet、Filter、Listener的繁琐步骤，允许开发人员使用注解修饰它们，从而进行部署。  
Servlet3.0规范在javax.servlet.annotation包下提供了如下注解：
- **@WebServlet**：用于修饰一个Servlet类，用于部署Servlet类  
- **@WebInitParam**：用于与@WebServlet或@WebFilter一起使用，为Servlet、Filter配置参数  
- **@WebListener**：用于修饰Listener类，用于部署Listener类  
- **@WebFilter**：用于修饰Filter类，用于部署Filter类  
- **@MultipartConfig**：用于修饰Servlet，指定该Servlet将会负责处理multipart/form-data类型的请求（主要用于文件上传）。  
- **@ServletSecurity**：这是一个与JAAS有关的注解，修饰Servlet指定该Servlet的安全与授权控制  
- **@HttpConstraint**：用于与@ServletSecurity一起使用，用于指定该Servlet的安全与授权控制  
- **@HttpMethodConstraint**：用于与@ServletSecurity一起使用，用于指定该Servlet的安全与授权  

## Servlet3.0的Web模块支持
Servlet 3.0为模块化开发提供了良好的支持，Servlet 3.0规范不再要求所有Web组件（如Servlet、Listener、Filter）都部署在web.xml文件中，而是允许采用“Web模块”来部署、管理它们。  
一个Web模块通常对应于一个JAR包，这个JAR包有如下文件结构：  
<webModule>.jar——这是Web模块的JAR包，可以改变  
|——META-INF  
|　　　　|  
|　　　　|——web-fragment.xml  
|——Web模块所用的类文件、资源文件等   

　　
从上面的文件结构可以看出，Web模块与普通JAR的最大区别在于需要在META-INF目录下添加一个Web-fragment.xml文件，这个文件也被称为Web模块部署描述符。  

web-fragment.xml文件与web.xml文件的作用、文件结构都基本相似，因为它们都用于部署、管理各种Web组件。只是web-fragment.xml用于部署、管理Web模块而已，但web-fragment.xml可以指定多个下面的两个元素：
- <name.../>：用于指定该模块的名称
- <ordering.../>：用于指定加载该Web模块的相对顺序
下面开发第一个Web模块，该Web模块内只定义一个简单的ServletContextListener，该模块对应的web-fragment.xml文件如下：
```
<?xml version="1.0" encoding="GBK"?>
<web-fragment xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee  
	http://xmlns.jcp.org/xml/ns/javaee/web-fragment_3_1.xsd" version="3.1">
	<!-- 指定该Web模块的唯一标识 -->
	<name>crazyit</name>
	<listener>
		<listener-class>lee.CrazyitListener</listener-class>
	</listener>
	<ordering>
		<!-- 用配置该Web模块必须位于哪些模块之前加载 -->
		<before>
			<!-- 用于指定位于其他所有模块之前加载 -->
			<others/>
		</before>
	</ordering>
</web-fragment>
```
上面的Web模块部署描述文件的根元素是web-fragment，粗体字代码指定该Web模块的名称是crazyiit，接下来的粗体字代码指定该Web模块将在其他所有Web模块之前加载  
接下来，再开发一个Web模块，接下来的Web模块同样只定义了一个ServletContextListener，该Web模块对应的web-fragment.xml文件如下：
```
<?xml version="1.0" encoding="GBK"?>
<web-fragment xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee  
	http://xmlns.jcp.org/xml/ns/javaee/web-fragment_3_1.xsd" version="3.1">
	<!-- 指定该Web模块的唯一标识 -->
	<name>leegang</name>
	<!-- 配置Listener -->
	<listener>
		<listener-class>lee.LeegangListener</listener-class>
	</listener>
	<ordering>
		<!-- 用配置该Web模块必须位于哪些模块之后加载 -->
		<after>
			<!-- 此处可用多个name元素列出该模块必须位于这些模块之后加载 -->
			<name>crazyit</name>
		</after>
	</ordering>
</web-fragment>
```
将这两个Web模块打包成JAR包  
将这两个Web模块对应的JAR包赋值到任意Web应用的WEB-INF/lib目录下，启动Web应用，将可以看到两个Web模块被加载：先加载crazyit模块，再加载leegang模块  
Web应用除了可按web-fragment.xml文件中指定的加载顺序来加载Web模块之外，还可以通过web.xml文件指定个Web模块加载的绝对顺序。在web.xml文件中指定的加载顺序将会覆盖Web模块中web-fragment.xml文件所指定的加载顺序。  
假如在Web应用的web.xml文件中增加如下配置片段：
```
<absolute-ordering>  
    <!-- 指定Web模块按如下顺序加载 -->  
    <name>moudle-name</name>  
    <name>moudle-name</name>  
</absolute-ordering>  
```
上面的配置片段指定了先加载leegang模块，后加载crazyit模块，如果重新启动该Web应用，可看到了leegang模块被优先加载  
Servlet 3.0的Web模块支持为模块化开发、框架使用提供了巨大的方便，例如需要在Web应用中使用Web框架，这就只要将该框架的JAR包复制到Web应用中即可。因为这个JAR包的META-INF目录下可以通过web-fragment.xml文件来配置该框架所需要的Servlet、Listener、Filter等，从而避免修改Web应用的web.xml文件。Web模块支持对于模块化开发也有很大的帮助，开发者可以将不同模块的Web组件部署在不同的web-fragment.xml文件中，从而避免所有模块的配置、部署信息都写在web.xml文件中，这对以后的升级、维护将更加方便。  
  
## Servlet3.0提供的异步处理
在之前的Servlet规范中，如果Servlet作为控制器调用一个耗时的业务方法，nameServlet必须等到业务方法完全返回之后才会生成响应，这将使得Servlet对业务方法的调用变成一种阻塞式的调用，因此效率比较低。  
Servlet 3.0规范引入了异步处理来解决这个问题，异步处理允许Servlet重新发起一条新线程去调用耗时的业务方法，这样就可避免等待。  
Servlet 3.0的异步处理是通过AsyncContext类来处理的，Servlet可通过ServletRequest的如下连个方法开启异步调用、创建AsyncContext对象：
- AsyncContext startAsync()
- AsyncContext startAsync(ServletRequest,ServletResponse)
重复调用上面的方法将得到同一个AsyncContext对象，AsyncContext对象代表异步处理的上下文，它提供了一些工具方法，可完成设置异步调用的超时时长，dispatch用于请求、启动后台线程、获取request、response对象等功能。  
示例：  
创建异步处理的Servlet类：
```
@WebServlet(urlPatterns = "/async", asyncSupported = true)  
public class AsyncServlet extends HttpServlet {  
    private static final long serialVersionUID = 1L;  
  
    @Override  
    public void doGet(HttpServletRequest request, HttpServletResponse response) throws IOException, ServletException {  
        response.setContentType("text/html;charset=GBK");  
        PrintWriter out = response.getWriter();  
        out.println("<title>异步调用示例</title>");  
        out.println("进入Servlet的时间：" + new java.util.Date() + ".<br/>");  
        // 创建AsyncContext，开始异步调用  
        AsyncContext actx = request.startAsync();  
        // 设置异步调用的超时时长  
        actx.setTimeout(60 * 1000);  
        // 启动异步调用的线程，该线程以异步方式执行  
        actx.start(new GetBooksTarget(actx));  
        out.println("结束Servlet的时间：" + new java.util.Date() + ".<br/>");  
        out.flush();  
    }  
}  
```
上面的Servlet类中创建了AsyncContext对象，并通过该对象以异步方式启动了一条后台线程。该线程执行体模拟调用耗时的业务方法，下面是线程执行体的代码：
```
public class GetBooksTarget implements Runnable {  
    private AsyncContext actx = null;  
    public GetBooksTarget(AsyncContext actx) {  
        this.actx = actx;  
    }  
    public void run() {  
        try {  
            // 等待5秒钟，以模拟业务方法的执行  
            Thread.sleep(5 * 1000);  
            ServletRequest request = actx.getRequest();  
            List<String> books = new ArrayList<String>();  
            books.add("疯狂Java讲义");  
            books.add("轻量级Java EE企业应用实战");  
            books.add("疯狂Ajax讲义");  
            request.setAttribute("books", books);  
            actx.dispatch("/async.jsp");  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
    }  
}  
```
该线程体让线程暂停5秒来模拟调用耗时的业务方法，最后调用AsyncContext的dispatch方法把请求dispatch到指定JSP页面。  
被异步请求dispatch的目标页面需要指定session=“false”，表明该页面不会重新创建session。下面是async.jsp页面代码： 
```
<%@ page contentType="text/html; charset=GBK" language="java"  
    session="false"  isELIgnored="false"%>  
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>  
<ul>  
    <c:forEach items="${books}" var="book">  
        <li>${book}</li>  
    </c:forEach>  
</ul>  
<%  
    out.println("业务调用结束的时间：" + new java.util.Date());  
    if (request.isAsyncStarted()) {  
        // 完成异步调用  
        request.getAsyncContext().complete();  
    }  
%>  
```
上面使用注解的方式已经配置好，如果要使用web.xml配置，则需要如下配置： 
```
<servlet>   
    <servlet-name>async</servlet-name>   
    <servlet-class>lee.AsyncServlet</servlet-class>   
    <async-supported>true</async-supported>   
</servlet>   
<servlet-mapping>   
    <servlet-name>async</servlet-name>   
    <url-pattern>/async</url-pattern>   
</servlet-mapping>  
```
对于支持异步调用的Servlet来说，当Servlet以异步方式启用新线程之后，该Servlet的执行不会被阻塞，该Servlet将可以向客户端浏览器生成响应——当新线程执行完成后，新线程生成的响应再次被送往客户端浏览器。  
  
当Servlet启用异步调用的线程之后，该线程的执行过程对开发者是透明的。但在有些情况下，开发者需要了解该异步线程的执行细节，并针对特定的执行结果进行针对性处理，这可借助于Servlet3.0提供的异步监听器来实现。  
异步监听器需要实现AsyncListener接口，实现该接口的监听器类需要实现如下4个方法：
- **onStartAsync(AsyncEvent event)**：当异步调用开始时触发该方法  
- **onComplete(AsyncEvent event)**：当异步调用完成时触发该方法  
- **onError(AsyncEvent event)**：当异步调用错误时触发该方法  
- **onTimeout(AsyncEvent event)**：当异步调用超时时触发该方法

接下来为上面的异步调用定义如下监听类：
```
public class MyAsyncListener
	implements AsyncListener
{
	public void onComplete(AsyncEvent event)
		throws IOException
	{
		System.out.println("------异步调用完成------" + new Date());
	}
	public void onError(AsyncEvent event)
		throws IOException
	{}
	public void onStartAsync(AsyncEvent event)
		throws IOException
	{
		System.out.println("------异步调用开始------" + new Date());
	}
	public void onTimeout(AsyncEvent event)
		throws IOException
	{}
}
```
上面实现的异步监听器类只实现了onStartAsync、onComplete两个方法，表明该监听器只能监听异步调用开始、异步调用完成两个事件。  
提供了异步监听器之后，还需要通过AsyncContext来注册监听器，调用该对象的addListener()方法即可。  
例如上面的Servlet中增加如下代码即可注册：
```
AsyncContext actx=request.startAsycn();
//为该异步调用注册监听器
actx.addListener(new MyAsyncListener());
```
虽然上面的例子都是基于Servlet的，但由于Filter与Servlet具有很大的相似性，因此Servlet3.0规范完全支持在Filter中使用异步调用。在Filter中进行异步调用与在Servlet中进行异步调用的效果完全相似。  
  
## 改进的Servlet API
重大的改进包括：
- HttpServletRequest增加了对文件上传的支持
- ServletContext允许通过编程的方式动态注册Servlet、Filter
HttpServletRequest提供了如下两个方法来处理文件上传：
- Part getPart(String name)：根据名称来获取文件上传域
- Collection<Part> getParts()：获取所有的文件上传域
上面两个方法的返回值都涉及一个API：Part，每个Part对象对应于一个文件上传域，该对象提供了大量方法来访问上传文件的文件类型、大小、输入流等，并提供了一个write(String file)方法将上传文件写入服务器磁盘。  
  

为了向服务器上传文件，需要在表单里使用<input type="file" ../>文件域，这个文件域会在HTML页面上产生一个单行文本框和一个“浏览”按钮，浏览者可通过该按钮选择需要上传的文件。除此之外，上传文件一定要为表单域设置enctype属性。  
  

表单的enctype属性指定的是表单数据的编码方式，该属性有如下三个值：
- **application/x-www-form-urlencoded**：这是默认的编码，它只处理表单域里的value属性值，采用这种编码方式的表单会将表单域的值处理成URL编码方式  
- **multipart/form-data**：这种编码方式会以二进制流的方式来处理表单数据，这种编码方式会把文件域指定文件的内容封装到请求参数里  
- **text/plain**：这种编码方式当表单的action属性为mailto:URL的形式时比较方便，这种方式主要适用于直接通过表单发送邮件的形式

如果将enctype设置为application/x-www-form-urlencoded，或不设置enctype属性，提交表单时只会发送文件域的文本框里的字符串，也就是浏览者所选择文件的绝对路径，对服务器获取该文件在客户端上的绝对路径没有任何作用，因为服务器不可能访问客户机的文件系统。  
  
示例如下：
下面定义了一个文件上传的页面：
```
<form method="post" action="upload"  enctype="multipart/form-data">  
    文件名：<input type="text" id="name" name="name" /><br/>  
    选择文件：<input type="file" id="file" name="file" /><br/>  
    <input type="submit" value="上传" /><br/>  
</form>  
```
上面的页面中的表单需要设置enctype=“multipart/form-data”，这表明该表单可用于上传文件。上面表单中定义了两个表单域：一个普通的文本框，它将生成普通请求参数；一个文件上传域，它用于上传文件。    
对于传统的文件上传需要借助于common-fileupload等工具，处理起来极为复杂，借助于Servlet 3.0的API，处理文件上传将变得十分简单。如下面的Servlet代码：
```
@WebServlet(name = "upload", urlPatterns = { "/upload" })  
@MultipartConfig  
public class UploadServlet extends HttpServlet {  
    private static final long serialVersionUID = 1L;  
    public void service(HttpServletRequest request, HttpServletResponse response) throws IOException, ServletException {  
        response.setContentType("text/html;charset=GBK");  
        PrintWriter out = response.getWriter();  
        request.setCharacterEncoding("GBK");  
        // 获取普通请求参数  
        String name = request.getParameter("name");  
        out.println("普通的name参数为：" + name + "<br/>");  
        // 获取文件上传域  
        Part part = request.getPart("file");  
        // 获取上传文件的文件类型  
        out.println("上传文件的的类型为：" + part.getContentType() + "<br/>");  
        // 获取上传文件的大小。  
        out.println("上传文件的的大小为：" + part.getSize() + "<br/>");  
        // 获取该文件上传域的Header Name  
        Collection<String> headerNames = part.getHeaderNames();  
        // 遍历文件上传域的Header Name、Value  
        for (String headerName : headerNames) {  
            out.println(headerName + "--->" + part.getHeader(headerName) + "<br/>");  
        }  
        // 获取包含原始文件名的字符串  
        String fileNameInfo = part.getHeader("content-disposition");  
        // 提取上传文件的原始文件名  
        String fileName = fileNameInfo.substring(fileNameInfo.indexOf("filename=\"") + 10, fileNameInfo.length() - 1);  
        // 将上传的文件写入服务器  
        part.write(getServletContext().getRealPath("/uploadFiles") + "/" + fileName); // ①  
    }  
}  
```
上面Servlet使用了@MultipartConfig修饰，处理文件上传的Servlet应该使用该注解修饰。  
接下来该Servlet中HttpServletRequest就可通过getPart(String name)方法来获取文件上传域——就像获取普通请求参数一样。   
上面的Servlet中将会把上传的文件保存到Web应用的根路径下的uploadFiles目录下，因此我们还需要在该Web应用的根目录下创建uploadFiles目录。  
上面Servlet上传时保存的文件名直接使用了上传文件的原始文件名，在实际项目中一般不会这么做，因为可能多个用户可能上传同名的文件，这样将导致后面用户上传的文件覆盖前面用户上传的文件。在实际项目中可借助于java.util.UUID工具类生成文件名。

> 提示：  
与Servlet3.0所有注解相似的是，Servlet3.0为@MultipartConfig提供了相似的配置元素，同样可以通过在<servlet.../>元素中添加<multipart-config.../>子元素来达到相同的效果  

ServletContext则提供了如下方法来动态地注册Servlet、Filter，并允许动态设置Web应用的初始化参数：
- 多个重载的addServlet()方法：动态地注册Servlet
- 多个重载的addFilter()方法：动态地注册Filter
- 多个重载的addListener()：动态地注册Listener
- setInitParameter(String name,String value)方法：为Web应用设置初始化参数


# Servlet 3.1新增的非阻塞式IO
Servlet3.1新特性包括强制更改session Id（由HttpServletRequest的changeSessionId()方法提供）、非阻塞IO等。尤其是Servlet3.1提供的非阻塞IO进行输入、输出，可以更好地提升性能。  
Servlet底层的IO是通过如下两个IO流来支持的：
- ServletInputStream：Servlet用于读取数据的输入流
- ServletOutputStream：Servlet用于输出数据的输出流
  
以Servlet读取数据为例，传统读取方式采用阻塞式IO——当Servlet读取浏览器提交的数据时，如果数据暂时不可用，或数据没有读取完成，Servlet当前所在线程将会被阻塞，无法继续向下执行。  
从Servlet 3.1 开始，ServletInputStream新增了一个setReadListener(ReadListener readListener)方法，该方法允许以非阻塞IO读取数据，实现ReadListener监听器需要实现如下三个方法：
- onAllDataRead()：当所有数据读取完成时激发该方法
- onDataAvailable()：当有数据可用时激发该方法
- onError(Throwable t)：读取数据出现错误时激发该方法

> 提示：  
类似地ServletOutputStream也提供了SetWriterListener()方法。通过这种方式，可以让ServletOutputStream以非阻塞IO进行输出

在Servlet中使用费阻塞IO非常简单，主要按如下步骤进行即可：
1. 调用ServletRequest的startAsync()方法开启异步模式
2. 通过ServletRequest获取ServletInputStream，并为ServletInputStream设置监听器（ReadListener实现类）
3. 实现ReadListener接口来实现监听器，在该监听器的方法中以非阻塞方式读取数据
  
下面是采用非阻塞IO进行读取的Servlet，示例如下：
```
@WebServlet(urlPatterns = "/async", asyncSupported = true)  
public class AsyncServlet extends HttpServlet {  
    private static final long serialVersionUID = 1L;  
    public void service(HttpServletRequest request, HttpServletResponse response) throws IOException, ServletException {  
        response.setContentType("text/html;charset=GBK");  
        PrintWriter out = response.getWriter();  
        out.println("<title>非阻塞IO示例</title>");  
        out.println("进入Servlet的时间：" + new java.util.Date() + ".<br/>");  
        // 创建AsyncContext，开始异步调用  
        AsyncContext context = request.startAsync();  
        // 设置异步调用的超时时长  
        context.setTimeout(60 * 1000);  
        ServletInputStream input = request.getInputStream();  
        // 为输入流注册监听器  
        input.setReadListener(new MyReadListener(input, context));  
        out.println("结束Servlet的时间：" + new java.util.Date() + ".<br/>");  
        out.flush();  
    }  
}  
```
上面程序调用request的startAsyc()方法开启异步调用之后，程序中input.setReadListener()为Servlet输入流注册了一个监听器，这样就无须在该Servlet中使用阻塞IO来获取数据了。而是改用由MyReadListener负责读取数据，这样Servlet就可以继续向下执行，不会因为IO阻塞线程。  
MyReadListener需要实现ReadListener接口，并重写它的三个方法。
```
public class MyReadListener implements ReadListener {  
    private ServletInputStream input;  
    private AsyncContext context;  
  
    public MyReadListener(ServletInputStream input, AsyncContext context) {  
        this.input = input;  
        this.context = context;  
    }  
  
    @Override  
    public void onDataAvailable() {  
        System.out.println("数据可用！！");  
        try {  
            // 暂停5秒，模拟读取数据是一个耗时操作。  
            Thread.sleep(5000);  
            StringBuilder sb = new StringBuilder();  
            int len = -1;  
            byte[] buff = new byte[1024];  
            // 采用原始IO方式读取浏览器向Servlet提交的数据  
            while (input.isReady() && (len = input.read(buff)) > 0) {  
                String data = new String(buff, 0, len);  
                sb.append(data);  
            }  
            System.out.println(sb);  
            // 将数据设置为request范围的属性  
            context.getRequest().setAttribute("info", sb.toString());  
            // 转发到视图页面  
            context.dispatch("/async.jsp");  
        } catch (Exception ex) {  
            ex.printStackTrace();  
        }  
    }  
  
    @Override  
    public void onAllDataRead() {  
        System.out.println("数据读取完成");  
    }  
  
    @Override  
    public void onError(Throwable t) {  
        t.printStackTrace();  
    }  
}  
```
上面程序中MyReadListener的onDataAvailable()方法先暂停线程5秒，用于模拟耗时操作，接下来程序使用普通IO流读取浏览器提交的数据。  
  
如果程序直接让Servlet读取浏览器提交的数据，那么该Servlet就需要阻塞5秒，不能继续向下执行；改为使用非阻塞IO进行读取，虽然读取数据的IO操作需要5秒，但是它不会阻塞Servlet执行，因此可以提升Servlet的性能。  
  
# Tomcat8的WebSocket支持
使用Tomcat8开发WebSocket服务端非常简单，大致有如下两种方式：
- 使用注解方式开发，被@ServerEndpoint修饰的java类即可作为WebSocket服务端  
- 继承Endpoint基类实现WebSocket服务端

开发被@ServerEndpoint修饰的Java类之后，该类中还可以定义如下方法。
- 被@OnOpen修饰的方法：当客户端与该WebSocket服务端建立连接时激发该方法  
- 被@OnClose修饰的方法：当客户端与该WebSocket服务端断开连接时激发该方法  
- 被@OnMessage修饰的方法：当WebSocket服务端收到客户端消息时激发该方法  
- 被@OnError修饰的方法：当客户端与该WebSocket服务端连接出现错误时激发该方法
