JSP(Java Server Page)和Servlet是JavaEE规范的两个基本成员。  

JSP和Servlet的本质是一样的，因为JSP最终必须编译成Servlet才能运行，或者说JSP只是生成Servlet的“草稿”文件。  

Web应用应该有如下结构：　　  
  
  
<webDemo>----这是web应用的名称，可以改变  
|——WEB-INF  
|　　　　|——classes  
|　　　　|——lib  
|　　　　|——web.xml  
|——<a.jsp>----此处可以存放任意多个JSP页面
  
  
## JSP的基本原理
JSP的本质是Servlet，当用户向指定Servlet发送请求时，Servlet利用输出流动态生成HTMl页面，包括每一个静态的HTML标签和所有在HTML页面中出现的内容。  
由于包括大量的HTML标签、大量的静态文本及格式等，导致Servlet的开发效率极为低下。所有的表现逻辑，包括布局、色彩及图像等，都必须耦合在Java代码中，这的确让人不胜其烦。JSP的出现弥补了各种不足。  
JSP通过在标准的HTML页面中嵌入Java代码，其静态的部分无须Java程序控制，只有那些需要从数据库读取或需要动态生成的页面内容，才使用Java脚本控制。  

JSP页面的内容由如下两部分组成：
- 静态部分：标准的HTMl标签、静态的页面内容，这些内容与静态HTMl页面相同。
- 动态部分：受Java程序控制的内容，这些内容由Java脚本动态生成。

  
从表面上看，JSP页面已经不再需要Java类，似乎完全脱离了Java面向对象的特征。事实上，JSP的本质仍然是Servlet（一个特殊的Java类），每个JSP页面就是一个Servlet实例——JSP页面由系统编译成Servlet，Servlet再负责相应用户请求。  
也就是说，JSP其实也是Servlet的一种简化，使用JSP时，其实还是使用Servlet，因为Web应用中的每个JSP页面都会由Servlet容器生成对应的Servlet。对于Tomcat而言，JSP页面生成的Servlet放在work路径对应的Web应用下。  
  
  
Tomcat根据JSP页面生成对应Servlet的Java文件和class文件。  

打开Java文件，会发现这就是一个Servlet类的源代码，该Java类主要包含如下三个方法：
- init():初始化JSP/Servlet的方法。
- destroy()：销毁JSP/Servlet之前的方法。
- service():对用户请求生成响应的方法。

所以，其实JSP页面的所有内容都由这个java文件的页面输出流来生成。  

### 结论
- JSP文件必须在JSP服务器内运行。
- JSP文件必须生成Servlet才能执行。
- 每个JSP页面的第一个访问者速度很慢，因为必须等待JSP编译成Servlet。
- JSP页面的访问者无须安装任何客户端，甚至不需要可以运行Java的运行环境，因为JSP页面输送到客户端的是标准HTMl页面。  
  

## JSp的4中基本语法
### JSP注释
JSP注释的格式：
```
<%-- 注释内容 --%>
```
HTMl注释的格式：
```
<!-- HTML注释部分--!>
```
HTML的注释可以通过源代码查看到，而JSP的注释是无法通过源代码查看到的。这表明JSP注释不会被发送到客户端。  

### JSP声明
JSP声明用于声明变量和方法。在JSP声明中声明方法看起来很特别，似乎不需要定义类就可以直接定义方法。  
实际上，JSP声明将会转换成对应Servlet的成员变量或成员方法，因此JSP声明依然符合java语法。  
JSP声明的语法格式如下：
```
<%! 声明部分 %>
```

> 提示：  
**由于JSP声明语法定义的变量和方法对应于Servlet类的成员变量和方法，所以JSP声明部分定义的变量和方法可以使用private、public等访问控制符修饰，也可使用static修饰，将其变成类属性和类方法。但不能使用abstract修饰声明部分的方法，因为抽象方法将导致JSP对应的Servlet变成抽象类，从而导致无法实例化。**

JSP页面会编译成一个Servlet类，每个Servlet在容器中只有一个实例；在JSP中声明的变量是成员变量，成员变量只在创建实例时初始化，该变量的值将一直保存，知道实例销毁。  

JSP声明的方法其实是JSP编译中生成的Servlet的实例方法——Java里的方法是不能独立的，即使在JSP页面中也不行。   

## 输出JSP表达式
语法格式：
```
<%=表达式%>
```
输出表达式的语法可以代替out.println输出语句。由此可见，输出表达式将转换成Servlet里的输出语句。  

> 注意：
**输出表达式语法后不能有分号**

## JSP脚本
格式：
```
<% java代码%>
```
通常来说，所有可执行Java代码都可通过JSP脚本嵌入HTML页面。  
  
JSP脚本将转换成Servlet里Service方法的可执行性代码。这意味着在JSP小教本部分也可以声明变量，但在JSP脚本部分声明的变量是局部变量，担不能使用private、public等访问控制符修饰，也不可使用static修饰。 
> **实际上不仅JSP小脚本部分会转换成_jspService方法里的可执行性代码，JSP页面里的所有静态内容都将由这个方法里的输出语句来输出，这就是JSP脚本可以控制JSP页面中静态内容的原因。由于JSP脚本将转换成_jspService方法里的可执行性代码，而Java语法不允许在方法里定义方法，所以JSP脚本里不能定义方法。**

因为JSP脚本中可以放置任何可执行性语句，所以可以利用Java语言功能，例如连接数据库和执行数据库操作。  


## JSP的3个编译指令
常见的编译指令有如下三个：
- page：该指令是针对当前页面的指令。
- include：用于指定包含另一个页面。
- taglib：用于定义和访问自定义标签。
  

使用编译指令的语法格式如下：
```
<%@ 编译指令名 属性名="属性值"···%>
```

### page指令
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
- Language：声明当前JSP页面使用的脚本语言的种类，因为页面是JSP页面，该属性的值通常是Java，该属性的默认值也是java，所以通常无须设置。  

- extends：指定JSP页面编译所产生的Java类所继承的父类，或所实现的接口。  
- import：用来导入包。不需显式导入的包有：java.lang.\*、javax.servlet.\*、javax.servlet.jsp.\*、javas.ervlet.http.*  

- session：设定这个JSP页面是否需要HTTP Session。  

- buffer：指定输出缓冲区的大小。out为输出缓冲区的JSP内部对象，用于缓存JSP页面对客户浏览器的输出，默认值为8KB。  

- autoFlush：当输出缓冲区即将溢出时，是否需要强制输出缓冲区的内容。true为正常输出；false则会在溢出时产生一个异常。  

- info：设置该JSP程序的信息。可以通过Servlet.getServletInfo()方法获取该值。如果在JSP页面中，可以直接调用getServletInfo()方法，因为JSP页面本身就是Servlet。  

- contentType：用于设定生成网页的文件格式和编码字符集，即MIME类型和页面字符集类型，默认的MIME类型是text/html：默认的字符集类型是ISO-8859-1.  

- pageEncoding：指定生成网页的编码字符集。

> 提示：
**errorPage属性的实质是JSP的异常处理机制，因为JSP內建了异常机制支持，所以JSP可以不处理异常，即使是checked异常。**


### include指令
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


## JSP的7个动作指令
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

### forward指令
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

  
### include指令
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

### useBean、setProperty、getProperty指令
这三个指令都是与JavaBean相关的指令。  
- useBean：用于在JSP页面初始化一个Java实例。
- setProperty：用于为JavaBean实例的属性设置值。
- getProperty：用于输出JavaBean实例的属性。

  
如果多个JSP页面中需要重复使用某段代码，则可以把这段代码定义成Java类的方法，然后让多个JSP页面调用该方法即可。  

useBean语法：
```
<jsp:useBean id="name" class="classname" scope="page|request|session|application"/>
```
其中，id属性是JavaBean的实例名，class属性确定JavaBean的实现类。scope属性用于指定JavaBean实例的作用范围，该范围有4个值：
- page：该JavaBean实例仅在该页面有效
- request：该JavaBean实例仅在本次请求有效
- session：该JavaBean实例在本次session内有效
- application：该JavaBean实例在本应用内一直有效

setProperty语法：
```
<jsp:setProperty name="BeanName" property="propertyName" value="value"/>
```
- name：确定需要设定JavaBean的实例名
- property：确定需要设置的属性名
- value：确定需要设置的属性值

getProperty语法：
```
<jsp:getProperty name="BeanName" property="propertyName"/>
```
- name：确定需要输出的JavaBean的实例名
- property：确定需要输出的属性名

对于setProperty和getProperty而言，它们都要求根据属性名来操作JavaBean的属性，而要求的属性名，与Java类中定义的属性有一定的差别。  
事实上，当页面使用setProperty和getProperty标签时，系统底层就是调用setXXX()和getXXX()方法来操作Java类的实例的属性。  
  
  
### plugin指令
plugin指令主要用于下载服务器端的JavaBean或Applet到客户端执行。由于程序在客户端执行，因此客户端必须安装虚拟机。  
> **实际由于现在很少使用Applet，而且就算要使用Applet，也完全可以使用支持Applet的HTMl标签，所以使用jsp:plugin标签的使用场景并不多。**

### param指令
param指令用于设置参数值，这个指令本身不能单独使用，可以与一下三个指令结合使用：
- jsp:include
- jsp:forward
- jsp:plugin

当与include指令结合使用时，param指令用于将参数传入被导入的页面；  
与forward指令结合时，param指令用于将参数值传入被转向的页面；  
与plugin结合时，param指令用于将参数传入页面中的JavaBean实例或applet实例。  
param语法：
```
<jsp:param name="paramName" value="value" />
```


  
## JSP脚本中的9个内置对象
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


### application对象
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
 
#### 让多个JSP、Servlet共享数据
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

#### 获得Web应用配置参数
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


### config对象
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

### exception对象
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

### out对象
out对象代表一个页面输出流。通常用于在页面上输出变量已经常量。一般在使用输出表达式的地方，都可以使用out对象来达到同样的效果。  

> 注意：  
**所有使用out的地方，都可使用输出表达式来代替，而且使用输出表达式更加简洁。<%=...%>表达式的本质就是out.print(..);。**


### pageContext对象
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

  
### request对象
request对象是JSP中的重要对象，每个request对象封装着一次用户请求，并且所有的请求参数都被封装在request对象中，因此request对象获取请求参数的重要途径。  
另外request代表本次请求范围，所以还可以用于操作request范围的属性。  

  
#### 获取请求头/请求参数
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

#### 操作request范围的属性
HttpServletRequest还包含如下两个方法，用于设置和获取request范围的属性。
- **setAttribute(String attName,Object attValue)**：将attValue设置成request范围的属性。
- **Object getAttribute(String attName)**：获取request范围的属性。  
  

> **forward用户请求时，请求参数和request范围的属性都不会丢失，即forward后还是原来的请求，并未再次向服务器发送请求。**  

#### 执行forward或include
request还有一个功能就是执行forward和include，也就是代替JSP所提供的forward和include动作指令。  

HttpServletRequest类提供了一个getRequestDispatcher（String path）方法，其中path就是希望forward或者include的目标路径,该方法返回RequestDispatcher，对象提供了如下两个方法。  
- **forward(ServletRequest request,ServletResponse response)**：执行forward。
- **include(ServletRequest request,ServletResponse response)**：执行include。