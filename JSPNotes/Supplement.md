## 使用地址重写的方法进行参数传递
参数不一定是全由表单传递的，也可以使用地址重写的方法进行参数传递，然后同样通过request对象的getParameter()方法获取参数的值  
格式：  
页面地址?参数名称1=参数值1&参数名称2=参数值2...  
```
<a href="test01.jsp?name=LinMin&sex=Man">测试</a>
```
## Cookie对象简介
cookie是保存再客户端本机硬盘的一段文本信息，通过cookie可以标识用户身份，记录用户名及密码等用户信息，跟踪用户。  
cookie在服务器端生成并发送给客户端的浏览器，浏览器将cookie的“键/值”信息保存到某个指定的文件夹中，cookie的名称和值可以由服务器端定义。  
  
与cookie相关的方法：  
- request对象的getCookie()方法：用于获取客户端设置的全部cookie对象集合  
- response对象的addCookie()方法：用于将cookie对象发送到客户端  
- cookie对象的构造方法：Cookie(String name,String value)，用于实例化cookie对象  
- cookie对象的setMaxAge()方法：设置cookie的有效作用时间，单位秒  
- cookie对象的getName()方法：获取客户端cookie的名称  
- cookie对象的getValue()方法：获取客户端cookie对象的值  

## 关于中文乱码问题的处理
在提交表单时，如果控件中输入的内容为中文，则会出现乱码，这种情况是编码不统一引起的，可以使用request对象的setCharacterEncoding()方法设置统一的编码（例如：request.setCharacterEncoding("UTF-8");），表单提交后就可以正常显示中文。  
  
如果使用地址重写方法传递参数，并且参数值为中文，也会出现乱码问题，例如：
```
<a href="test02.jsp?name=李民&sex=男">测试</a>
```
此时可以将参数值进行编码转换
```
<%
String name=new String(request.getParameter("name").getBytes("ISO8859_1"),"UTF-8");
String sex=new String(request.getParameter("sex").getBytes("ISO8859_1"),"UTF-8");
```
另外，对JSP页面，在超链接中参数传递的值为中文时，可以使用java.net.URLEncoder.encode()方法进行编码处理，可以避免产生中文乱码  
例如：  
```
<a href="test02.jsp?name=<%=URLEncoder.encode('李民')%>">测试</a>
```
  
## 关于JSP页面中超链接的访问路径
如果JSP页面中有一个超链接，其完整的访问路径为：http://localhost:8080/project01/task1-2.jsp。  
其中，http://localhost:8080/是服务器的基本路径，project01是当前应用程序项目名称，根路径是http://localhost:8080/project01/  
如果页面中有多个超链接，则需要多次重复书写根路径，我们可以使用以下方法指定公用的根路径，然后与页面内的相对路径进行拼装  
使用request对象的多个方法获取当前应用的根路径：  
```
<%
String path=request.getContextPath();
String basePath=request.getScheme()+"://"+request.getServerName()+":"+reqeust.getServerPort()+path+"/";
%>
```
方法getContextPath()获取当前页面所在项目的名称，本实例为project01，如果项目为根目录，则得到一个""，即空字符串。  
- request.getScheme()返回当前页面使用的协议，本实例为http  
- request.getServerNmae()返回当前页面所在服务器的名称，本实例为localhost  
- reque.getServerPort()返回当前页面所在服务器使用的端口，本实例为8080  
将4个方法获取的字符串进行拼接，就是当前应用的根路径  
在页面<head>里面添加以下代码：
```
<base href="<%=basePath%>">
```
有了这个<base..>标签以后，页面内部的超链接，就可以不写全路径，只需写task1-2.jsp即可。  
  
  
## 关于静态网址中获取表单控件的值并作为参数进行传递
自定义一个函数redirect()，该函数获取表单控件的值，并带参数重定向网页：
```
<script type="text/javascript">
    function redirect(){
        var username;
        username=form1.username.value;
        location.reload("test02.jsp?name"+username);
    }
</script>
```
然后在链接地址中调用该自定义函数即可
```
<a href="javascript:redirect();">测试</a>
```
  
    
## 比较response.sendRedirect()跳转方法和<jsp:forward>标签跳转指令
区别：  
- response对象的sendRedirect()方法可以实现页面的跳转，该跳转属于客户端跳转，跳转后地址栏的地址会发生改变，变为跳转之后的页面地址。<jsp:forward>跳转属于服务器端跳转，跳转之后地址栏中的地址不会发生改变  
- 客户端跳转，在整个页面都执行完成后才进行跳转，而服务器跳转，执行到跳转语句时会立刻进行跳转  
- 使用sendRedirec()方法时，可以通过地址重写的方式完成参数的传递，使用<jsp:forward>时,可以通过<jsp:param>方式进行参数的传递  
- 使用request对象时，如果是客户端跳转，则request对象中的属性值全部失效，并且进入一个新request对象的作用域，只有服务器端跳转才能将request对象的属性保存到跳转页  

## getParameter()方法和getParameterValues()方法的正确使用
在一个表单会有不同的表单控件元素，对于文本框、单选按钮、下拉列表框都可以使用getParameter()方法获取其值，该方法接受的是一个参数的内容，参数的名称就是表单控件的名称  
对于复选框及多选列表框被选定的内容要使用getParameterValues()方法获取，该方法返回一个字符串数组，数组的大小和内容取决于用户的选择，通过循环遍历整个数组就可以得到用户选定的所有内容  
如果用户没有在文本框或多选列表中输入内容或者没有选择复选框，那么在使用getParameter()方法和getParameterValues()时，返回的内容为null，那么此时可能会产生NullPointerException异常，所以在使用时应判断接受的参数是否为null  
  
## Servlet的主要特点
- Servlet是运行在Web服务器上的Java应用程序，与普通的Java应用不同的是，它位于Web服务器端，可以对浏览器或其他HTTP客户端程序发送的请求进行处理并作出响应，将处理结果返回客户端  
- Servlet采用Java语言编写，继承了Java语言的诸多优点，同时还对Java的Web应用进行了扩展，它具有方便实用的API方法、高效的处理方式、跨平台、可移植性好、更加灵活、安全性高等特点  
- Servlet通过HttpServletRequest接口和HttpServletResponse接口对HTTP请求进行处理及响应，可以在处理业务逻之后，将动态内容返回并输出到HTML页面中，与用户请求进行交互。  
- Servlet还提供了强大的过滤器功能，可以针对请求类型进行过滤设置，为Web应用开发提供了灵活性和扩展性

  
## Servlet与JSP的比较
Servlet是一种在服务器端运行的Java应用程序，它先于JSP产生。  
JSP是一种在Servlet规范之上的动态网页技术，通过JSP页面嵌入Java代码，可以实现动态网页。可以将其理解为时Servlet技术的扩展，在JSP文件被第一次请求时，它会被编译成Servlet文件，再通过服务器调用Servlet进行处理。  
区别：  
- Servlet承担客户端请求与业务处理的中间角色，需要调用固定的方法，将动态程序代码混合到静态的HTML代码中；而在JSP页面，则可以直接使用HTML标签进行输出  
- Servlet需要调用Servlet API接口处理HTTP请求；而在JSP页面中，则可以直接使用内置对象进行处理  
- Servlet的使用需要进行一定的配置，而JSP文件通过.jsp扩展名部署在容器中，容器对其自动识别，直接编译成Servlet进行处理  
  
  
## Servlet的生命周期
包括加载和实例化、初始化、处理请求和释放占用资源4个阶段  
1)加载和实例化
- Servlet容器负责加载和实例化Servlet，当客户端发送一个请求时，Servlet容器会查找内存中是否存在Servlet实例，如果不存在，就创建一个Servlet实例。如果存在Servlet实例，直接从内存中取出该实例来响应请求
- Servlet容器也称为Servlet引擎，通常情况是Web服务器或应用服务器的一部分，用于在发送的请求和作出的响应之间提供网络服务
  
2)初始化
- Servlet容器加载完Servlet后，必须进行初始化。初始化Servlet时，可以设置数据库连接参数，建立JDBC连接，或者建立对其他资源的引用等。在初始化阶段，调用init()方法完成Servlet的初始化操作
  
3)处理请求
- Servlet被初始化后，就处于能响应请求的就绪状态。容器通过Servlet对象的service()方法处理客户端请求。
- 每个对Servlet的请求都由一个ServletRequest对象代表，Servlet给客户端的响应由一个ServletResponse对象代表，这两个对象以参数形式传给service()方法，在service()内，对客户端的请求方法进行判断，如果为GET方法提交，则调用doGet()方法处理请求，如果为POST方法提交，则调用doPost()方法处理请求
  
4)释放占用资源
- Servlet容器判断一个Servlet应用释放时(容器关闭或需要回收资源)，容器必须让Servlet释放其正在使用的资源。这些都是由容器调用Servlet对象的destroy()方法实现。destroy)()方法只是指明哪些资源可以被系统回收，最终被垃圾回收器回收

  
## Servlet处理的基本流程
Servlet主要运行在服务器端，并由服务器调用执行以处理客户端的请求，并作出响应。一个Servlet就是一个Java类，更直接地说，Servlet是能够使用print语句产生动态HTML内容的Java类。  
Servlet处理的基本流程：
1. 客户端(浏览器)通过HTTP提出请求
2. Web服务器接收该请求并将其发送给Servlet，如果这个Servlet尚未被加载，Web服务器将把其加载到Java虚拟机并且执行它
3. Servlet程序将接收该HTTP请求并执行某种处理
4. Servlet会将处理后的结果向Web服务器返回应答
5. Web服务器将从Servlet的应答发回给客户端
  
  
## Servlet基本代码结构
通常所说的Servlet是指HttpServlet对象，在声明一个对象为Servlet时，需要继承HttpServlet类。HttpServlet类是Servlet接口的一个实现类，继承此类后，可以重写HttpServlet类中的方法对HTTP请求进行处理  

当服务器引擎第一次收到Servlet请求时，会使用init()方法初始化一个Servlet对象，以后每当服务器再接收到一个Servlet请求时，就会产生一个县城，并在线程中调用service()方法检查HTTP请求类型是GET还是POST，同时根据用户的请求方式，对应地调用doGet()方法或者doPost()方法。  
因此在Servlet类中，不必重写service()方法来响应客户端的请求，直接继承service()方法即可。但可以重写doGet()和doPost()方法来响应，这样不仅增加响应的灵活性，而且降低了服务器的负担  
  
## Servlet接口和ServletConfig接口
1)Servlet接口  
在Servlet编程时，Servlet API提供了标准的接口与类，它们为HTTP请求与程序响应提供了丰富的方法。Servlet的运行需要Servlet容器的支持，Servlet容器调用Servlet对象的方法对请求进行处理。  
在Servlet应用程序开发中，一个Servlet对象都要直接或间接地实现javax.servlet.Servlet接口，该接口中包含了5个方法  
除了init()、service()、destroy()还有getServletConfig()和getServletInfo()  
- getServletConfig()用于获取Servlet对象的配置信息，返回ServletConfig对象
- getServletInfo()返回有关Servlet的信息，它是纯文本格式的字符串
  
2)ServletConfig接口
ServletConfig接口位于javax.servlet包中，它封装了Servlet的配置信息，在Servlet初始化期间被传递。每个Servlet对象只有一个ServletConfig对象，该对象定义了4个方法，getInitParameter()、getInitParameterNames()、getServletContext()和getServletName()  
  
## GenericServlet类和HttpServlet类
1)GenericServlet类  
创建一个Servlet对象时，必须实现javax.servlet.Servlet接口，由于Servlet接口中包含了5个方法，所以创建Servlet对象时要实现这5个方法，这样很不方便  
javax.servlet.GenericServlet类简化了操作，实现了Servlet接口，其原型如下：
```
public abstract class GenericServlet extends Object
                                    implements Servlet,ServletConfig,Serializable
```
GenericServlet类是一个抽象类，分别实现了Servlet接口和ServletConfig接口。该类实现了除service()之外的其他方法，在创建Servlet对象时，可以继承GenericServlet类来简化程序中的代码，但仍需要实现service()方法  
2)HttpServlet类  
GenericServlet类虽然实现了javax.servlet.Servlet接口，为javaweb应用程序的开发提供了方便，但是在实际开发过程中，大多数的应用都是使用Servlet处理HTTP协议的请求，并对请求作出响应，所以通过继承GenericServlet类仍然不是很方便  
javax.servlet.http.HttpServlet类继承了GenericServlet类，并对GenericServlet类进行了扩展，为HTTP请求的处理提供了灵活的方法，可以很方便地对HTTP请求进行处理及响应，其原型如下：
```
public abstract class HttpServlet extends GenericServlet 
                                implements Serializable
```
HttpServlet类仍然是一个抽象类，实现了service()方法，并针对HTTP1.1中定义的7种请求类型提供了相应的方法，分别为doGet()、doPost()、doPut()、doDelete()、doHead()、doOptions()和doTrace()。在7个方法中，除了对doOptions()和doTrace()方法进行简单实现外，HttpServlet类并没有对其他方法进行实现，需要开发人员重写  
  
    
      
## HttpServletRequest接口与HttpServletResponse接口
1)HttpServletRequset接口    
HttpServletRequest接口位于javax.servlet.http包中，继承了javax.servlet.ServletRequest接口，是Servlet中的重要对象，其常用方法有：
- getContextPath()、getCookies()、getMethod()、getQueryString()、getRequestURL()、getServeltPath、getSession()。  
2)HttpServletResponse接口   
HttpServletResponse接口位于javax.servlet.http包中，继承ljavax.servlet.ServletResponse接口，常用方法：
- addCookie()、sendError()、sendRedirect()  


## 关于web.xml文件
Servlet作为一个组件，需要部署到tomcat中才能正常运行，因为所有的Servlet程序都以.class的形式存在，所以必须在web.xml文件中进行Servlet程序的映射配置  
web.xml文件经常包含servlet-mapping和servlet等多个XML元素，其中servlet-mapping将用户访问的URL映射到Servlet的内部名，servlet元素把Servlet内部名映射到一个Servlet类名(包名称.类名称)  
当客户端发送一个请求的URL到<servlet-mapping>中的<url-pattern>值的时候容器会根据响应的<servlet-name>值，在<servlet>元素范围内查找与<servlet-name>对应的<servlet-class>类，然后去执行该类的doGet()方法或者doPost()方法  
    
      
## 表单中action属性的正确设置
在实际开发中，经常会出现找不到Servlet而报的404错误，出现这种问题，是由于提交后的路径与web.xml文件中的配置路径不一致造成的，可以在表单中将action属性设置为“<%=request.getContentPath()%>/Servlet类名”。  
在eclipse中，如果JSP页面位于文件夹WebContent的子文件夹中，则应将action属性设置为“<%=request.GetContentPath()%>/路径/Servlet类名”  
  
## Filter API
1)Filter接口  
Filter接口位于javax.servlet包中，与Servlet接口相似，当定义一个过滤器对象时需要实现此接口。  
在Filter接口中包含了3个方法，分别为init()、doFilter()和destroy()。doFilter()方法与Servlet中的service()方法类似，当请求及响应交给过滤器时，过滤器调用次方法进行过滤处理。  
2)FilterChain接口  
FilterChain接口位于javax.servlet包中，该接口由容器进行实现。FilterChain接口只包含一个方法doFilter()，该方法主要用于将过滤处理的请求或响应传递给下一个过滤器对象。在多个过滤器的Web应用中，可以通过此方法进行过滤传递  
3)FilterConfig接口
FilterConfig接口位于java.servlet包中，该接口由容器进行实现，用于获取过滤器初始化期间的参数信息，包含的方法有getFilterName()、getInitParameter()、getInitParameterNames()和getServletContext()  
  
