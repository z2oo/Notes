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
在使用include动作指令(即<JSP:include page="XXX.jsp"/>)来include其他html页面或JSP页面时，通常如果被include的页面中含有中文，则会出现乱码  
可以将被include的文件改为JSP页面，然后再在这个JSP页面头一行加上
```
<%@page contentType="text/html;utf-8" pageEncoding="utf-8"%>
```
就可以解决乱码，然而如果是HTML页面的话则不行，可以换一种include方式，即静态导入
```
<%@include file="XXX.html"%>
```
然后再在html页面的头部加上
```
<%@page  pageEncoding="utf-8"%>
 ```
尽管尽管html不能识别该指令，但通过include指令引入时该指令就能起作用了  
  

 
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
  
## JDBC的实现原理
JDBC主要通过java.sql包提供的API供Java程序开发者使用，驱动程序厂商则通过实现折现接口封装各种对数据库的操作。JDBC为多种关系数据库提供了统一访问接口，它可以向相应数据库发送SQL调用，将Java语言和JDBC结合起来，程序员只需编写一次程序就可以让它在任何平台上运行  
JDBC可以说是程序开发者和数据库厂商之间的桥梁，Java程序开发者和数据库厂商在统一的JDBC标准之下，负责各自的工作范围。同时，任何一方的改变对另一方都不会造成显著的影响  
JDBC的作用概括起来包括以下几方面：
- 建立与数据库的连接
- 向数据库发出查询请求
- 处理数据库的返回结果
  
  
## 使用JDBC访问数据库
1)注册与加载连接数据库的驱动程序  
使用Class.forName(JDBC驱动程序类)的方式显式加载一个驱动程序类，使用DriverManager类的getConnection()方法建立与数据库的连接  
驱动程序负责向DriverManager类登记注册，在与数据库相连接时，DriverManager将使用该驱动程序  
基本格式为Class.forName("JDBC驱动程序类");  
连接SQL Server的驱动程序的示例代码如下：
```
Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
```
连接Oracle的驱动程序代码：
```
Class.forName("oracle.jdbc.driver.OracleDriver");
```
2)创建与数据库的连接  
创建与数据库的连接要用到java.sql.DriverManager类和java.sql.Connection接口  
3)通过连接对象获取指令对象  
JDBC提供了3个类用于向数据库发送SQL语句，Connection接口中的3个方法可用于创建这些类的实例，分别是Statement、PreparedStatement和CallableStatement  
4)使用指令对象执行SQL语句  
5)获取结果集，且对结果集做相应处理  
6)释放资源  
  
  
## JDBC的DriverManager类
DriverManager类是java.sql包中用于管理数据库驱动程序的类，根据数据库的不同，注册、装载相应的JDBC驱动程序，JDBC驱动程序负责直接连接相应的数据库  
在DriverManager类中存有已注册的驱动程序清单，当调用DriverManager类的方法getConnection时，它将检查清单中的所有驱动程序，一直找到可与URL中指定的数据库进行连接的驱动程序为止。  
只要加载了合适的驱动程序，DriverManager对象就开始管理连接  
使用DriverManager类的getConnection()方法建立与数据库的连接，并返回一个Connection对象，此方法的参数包括目的数据库的URL、数据库用户名和密码  
```
static Connection getConnection(String url,String username,String password)
基本格式：Connection conn=DriverManager.getConnection("JDBC URL","数据库用户名","密码");
```
  
## JDBC的Connection接口
Connection接口负责连接数据库并完成传送数据的任务，与特定数据源建立连接是进行数据库访问操作的前提。一个Connection对象代表与数据库的一个连接。连接过程包括执行的SQL语句和在该连接上所返回的结果。只有在成功建立连接的前提下，SQL语句才可能被传递到数据库，最终被执行并返回结果  
Connection接口的主要方法如下
- Statement createStatement()：创建一个Statement对象
- Statement createStatement(int resultSetType,int resultSetConcurrency)：创建一个Statement对象，它将生成具有特定类型和并发性的结果集  
- void commit()：提交对数据库的改变并释放当前持有的数据库的锁  
- void rollback()：回滚当前事务中所有改变并释放当前连接持有的数据库的锁  
- boolean isClose()：判断连接是否已关闭  
- boolean isReadOnly()：判断连接是否为只读模式  
- void clearWarning()：清除连接的所有警告信息  
- void close()：立即释放连接对象的数据库和JDBC资源  
  
  
## JDBC的Statement接口
Statement接口由Connection接口产生，用于在已经建立的连接的基础上向数据库发送SQL语句，包括查询、新增、修改和删除等操作  
Statement对象使用Connection的createStatement方法创建，用来执行静态的SQL语句并返回执行的结果  
```
Statement stmt=conn.createStatement();
```
如果要建立可滚动的记录集，需要使用如下格式：
```
public Statement createStatement(int resultSetType,int resultSetConcurrency
```
Statement接口提供了3中执行SQL语句的方法：executeQuery、executeUpdate和execute：
1. ResultSet executeQuery(String strSql)
- 这种方法的执行结果将返回单个结果集，主要用于在Statement对象中执行SQL查询语句，并返回查询生成的ResultSet对象  
2. int executeUpdate(StringstrSql)
- 这种方法用于执行Insert、Update、Delete和SQL DDL(数据定义语言)语句，返回一个整数值，表示执行SQL语句影响的数据行数
```
String strSql="Update 用户表 Set 密码='"+password+"'Where 用户编号='"+code+"'";
int num=statement.executeUpdate(strSql);
```
3. boolean execute(String sql)
- 这种方法是执行SQL语句调用的一般方法，允许用户执行SQL数据定义命令，然后获取一个布尔值，显示是否返回ResultSet对象。用于执行返回多个结果集、多个更新结果或两者组合的语句
  
Statement对象将由Java垃圾收集程序自动关闭。而作为一种良好的编程风格，应该在不需要Statement对象时显式地关闭它们  
  
  
## JDBC的ResultSet接口
ResultSet接口负责保存Statement执行后返回的查询结果。ResultSet对象实际上是一个由查询结果数据构成的表，在ResultSet中隐含了一个指针，利用这个指针移动数据行，可以取得所要的数据，或者对数据进行简单的操作    
JDBC的ResultSet对象包含了执行某个SQL语句后返回的所有行，表示返回结果集的数据表，该结果集可由Statement对象、PrepareStatement对象或者CallableStatement对象执行SQL语句后返回。ResultSet对象提供了对这些结果集中的访问，在每个ResultSet对象的内部就好像有一个指针，借助于指针的移动，就可以遍历ResultSet对象内的每个数据项。  
因为一开始指针所指向的是第一行记录之前，所以必须首先调用next()方法才能取出第1条记录，而第二次调用next()方法时指针就会指向第2条记录  
在ResultSet接口中，提供了一系列方法在记录集中自由移动记录指针：
- void first()：将记录指针移动到记录集的第一行  
- void last():将记录指针移动到记录集的最后一行
- void previous()：将记录指针从当前位置向前移动一行
- void next()：将记录指针从当前位置向后移动一行
- void beforeFirst()：将记录指针移动到记录集的第一行之前
- void afterLast()：将记录指针移动到记录集的最后一行之后
- boolean absolute(int row)：将记录指针移动到记录集中给定编号的行
- boolean isFirst()：如果记录指针位于记录集的第一行，则返回true，否则返回false
- boolean isLast()：类似上
- boolean isBeforeFirst()：如果记录指针位于记录集的第一行之前，返回true
- boolean isAfterLast()：类似上
- int getRow()：获取当前行的编号
  
SQL数据类型与Java数据类型并不完全匹配，需要一种转换机制，通过ResultSet对象提供的getXXX()方法，可以取得数据项内的每个字段的值(XXX代表对应字段的数据类型，如getInt()、getString()、getDouble、getBoolean()、getDate()、getTime()等)，可以使用字段的索引或字段的名称获取值    
一般情况下，使用字段的索引，字段索引从1开始编号。  
假设ResultSet对象内包含两个字段，分别为整型和字符串类型，则可以使用rs.getInt(1)与rs.getString(2)方法来取得这两个字段的值(1、2分别代表各字段的相对位置)。当然也可以使用列的字段名来指定列，如rs.getString("name")  
  
  
## JDBC的PreparedStatement接口
PreparedStatement接口继承自Statement接口，PreparedStatement实例包含已编译的SQL语句，其执行速度要快于Statement对象  
PreparedStatement对象是使用Connection对象的PreparedStatement()方法创建的
```
String strSql="select 用户编号 from 用户表 where 用户编号=?";
PreparedStatement ps=conn.prepareStatement(strSql);
ps.setString(1,code);   //code变量中存入了用户编号值
ResultSet rs=ps.executeQuery();
```
PreparedStatement对象中药执行的SQL语句可包含1个或多个输入参数，参数的值在SQL语句创建时未被指定，而为每个参数保留了一个占位符“?”，在执行PreparedStatement对象之前，必须设置每个占位符“?”参数的值，可以通过调用setXXX()方法来完成，其中XXX表示该参数相应的类型。  
  
PreparedStatement接口继承自Statement接口的3种方法：execute()、executeQuery()和executeUpdate()，但这3种方法不需要参数。其中execute()方法用于在PreparedStatement对象中执行SQL语句，该SQL语句可以是任何种类的SQL语句  
executeQuery()方法用于在PreparedStatement对象中执行SQL查询语句，并返回该查询生成的ResultSet对象  
executeUpdate()方法用于在PreparedStatement对象中执行SQL语句，该SQL语句必须是一个SQL数据操作语言语句，如Insert、Update、Delete语句，或者是无返回内容的SQL语句，如DDL语句  
   
     
       
##　EL表达式语言
为了能够获取Web应用程序中的相关数据，EL表达式中定义了多个隐含对象。PageContext隐含对象用于访问JSP内置对象，如request、response、out、session、config等，例如要获取当前session中的变量，可以
```
${PageContext.session.name}
```
EL表达式中提供了4个用于访问作用域范围的隐含对象，即pageScope、requestScope、sessionScope、applicationScope，这4个隐含对象只能用来取得指定范围内的属性值，而不能取得其他相关信息。   
应用这4个隐含对象指定查询标识符的作用域后，系统将不再按照默认的顺序(page、request、session、application)来查找相应标识符。例如，要获取request范围内的goodsName变量
```
${requestScope.goodsName}
```
  
    
## JavaBean简介
Java Web应用程序设计时使用JavaBean可将Web程序的业务逻辑代码与HTML代码分离，使之成为独立可重复使用的模块，从而实现代码的重用及程序维护的方便  
JavaBean是一种可重复使用、跨平台的软件组件，在JSP页面中通过特定的JSP标签来访问JavaBean，其可用于多个Web组件进行共享  
在JSP页面中，可以通过JSP提供的动作标签来操作JavaBean对象，主要包括<JSP:useBean>、<JSP:setProperty>、和<JSP:getProperty>3个动作标签，这3个标签为JSP内置的动作标签，在使用过程中，不需引入第三方的类库  
JavaBean实际上就是一个Java类，这个类可以重用，可以很好地实现HTML代码与业务逻辑的分离。定义JavaBean的基本要求如下：
- 所有的Java类必须放在一个包中  
- 所有的Java类必须声明为public类型，这样才能被外部访问  
- 类中所有的属性都必须封装，即使用private声明  
- 封装的属性如果需要被外部所操作，则必须编写对应的setXXX()方法和getXXX()方法  
- 一个JavaBean中至少存在一个无参构造方法，为JSP中的标签所使用  
  
  
## JSP操作JavaBean对象的动作标签
### <jsp:useBean>动作标签
<JSP:useBean>动作标签用于在JSP页面只创建一个JavaBean实例，并通过属性的设置将该实例存放到JSP指定的范围内。语法：
```
<jsp:useBean id="实例化对象名称" scope="作用域" class="包名称.类名称">
</jsp:useBaen>
```
属性名称|属性说明
--|--
id|表示实例化对象的名称，程序中通过该名称对JavaBean进行引用
scope|设置JavaBean的作用域，分别为page、request、session和application，默认为page
class|指定JavaBean的完整类名，由包名与类名结合

### <jsp:setProperty>标签
<JSP:setProperty>标签用于给JavaBean的属性赋值，要求JavaBean相应的属性要提供setXXX()方法。通常情况下，该标签与<JSP:useBean>标签配合使用。语法：
```
<jsp:setProperty name="实例化对象的名称" property="属性名称" value="属性值" param="参数名" />
```
属性名|属性说明
--|--
name|指定JavaBean的引用名称，即<jsp:useBean>标签中的id属性值，该实例对象必须在其前面使用<jsp:useBean>定义才可以使用
property|指定需要设置的JavaBean中的属性名称，该属性是必须的，取值有“*”和“JavaBean中的属性名称”
value| 指定JavaBean中属性值
param|指定JSP请求中的参数名，通过该参数可以将JSP请求参数的值赋给JavaBean中的属性

<jsp:setProperty>标签中的property、value和param结合使用，根据这3个属性的不同取值，<JSP:setProperty>标签有4种用法：  
类型|属性设置格式|使用说明
--|--|--
自动匹配|property="*"|当HTML表单中空间的name属性值与JavaBean中的属性名一致时，可以使用自动匹配方式，自动调用JavaBean中的setXXX()方法为属性赋值，否则不能赋值
指定属性|property="属性名称"|当HTML表单中控件的name属性值与JavaBean中的属性名一致时，为名称相同的JavaBean属性赋值，否则不赋值
指定内容|property="属性名称" value="属性值"|将一个指定的属性值直接赋给JavaBean中指定的属性
指定参数|property="属性名称" param="参数名" |将JSP请求中request对象参数的值赋JavaBean中指定的属性

  
### <jsp:getProperty>标签
<jsp:getProperty>标签用于获取JavaBean中的属性值，但要求JavaBean的属性必须具有相对应getXXX()方法。语法：
```
<jsp:getProperty name="实例化对象的名称" property="属性名称"/>
```
其中，name属性指定JavaBean的引用名称，property属性指定JavaBean的属性名称
  
   
     
## JavaBean的作用域
JavaBean的作用域有4种，分别为page、request、session和application，默认情况下为page。  
通过<jsp:useBean>标签的scope属性进行设置，这3种作用域与JSP页面中的page、request、session和application的作用域向对应
选项名称|作用域说明
--|--
page|有效范围为客户请求访问的当前JSP页面，以下两种情况会结束其生命周期：1)客户请求访问的当前JSP页面通过<forward>标签请求转发到另一个JSP页面；2)客户请求访问的当前JSP页面执行完毕并向客户端发回响应
request|有效范围为：1)客户请求访问的当前JSP网页；2)和当前JSP页面共享和当前JSP页面共享同一个客户请求的页面，即当前JSP页面中使用<%@include>标签、<jsp:include>标签和<forward>标签包含的其他JSP页面。　　当所有共享同一个客户请求的JSP页面执行完毕并向客户端发回响应时，JavaBean对象结束生命周期。　　　JavaBean对象作为属性保存在HttpRequest对象中，属性名为JavaBean的id，属性值为JavaBean对象，因此也可以通过HttpRequest.getAttribute()方法取得JavaBean对象
session|JavaBean对象被创建后，它存在于整个session的声明周期内，同一个session中的JSP页面共享整个JavaBean对象。当session超时或会话结束时JavaBean被销毁。　　JavaBean对象作为属性保存在HttpSession中，可通过HttpSession.getAttribute()方法取得JavaBean对象
application|存在于整个Web应用的生命周期内，Web应用中的所有JSP页面都能共享同一个JavaBean对象。知道服务器关闭时JavaBean才被销毁可通过application.getAttribute()方法取得JavaBean对象

JavaBean的4种作用域与JavaBean的生命周期息息相关，当JavaBean被创建之后，通过<jsp:setProperty>标签和<jsp:getProperty>标签调用时，将会按照page、request、session和application顺序来查找这个JavaBean实例对象，直到找到一个实例对象为止，如果找不到，最终会抛出异常  

  
## Model2开发模式
Model2模式在JSP+JavaBean的模式基础上引入了Servlet技术。这种开发模式遵循MVC的设计理念：
- JSP作为视图为用户提供了程序交互的界面
- JavaBean作为模型封装实体对象及业务逻辑
- Servlet作为控制器接收各种业务请求，并调用JavaBean模型组件对业务逻辑进行处理
  
  
## MVC设计原理
MVC是一种经典的程序设计理念，该模式将应用程序分为3个部分，分别为模型(Model)、视图(View)和控制(Controller)。  
### 模型(Model)
模型是应用的核心部分，包括业务逻辑层和数据访问层。在JavaWeb应用程序中，业务逻辑层一般有JavaBean或EJB来充当，可以是一个实体对象或一种业务逻辑。  
数据访问层(数据持久层)通常应由JDBC或者Hibernate来实现，主要负责与数据库打交道，如从数据库中取出数据，向数据库中保存数据等。  
之所以称之为模型，是因为它在应用程序中有更好的重用性和扩展性
  
### 视图(View)
视图提供应用程序与用户之间的交互界面，即JavaWeb应用程序的外观。在MVC模式中，这一层不包含业务逻辑，只提供一种与用户交互的视图，在Web应用程序中由JSP、HTML页面充当。  
视图可以接受用户的输入，但并不包括任何业务处理，只是将数据转交给控制器  
  
### 控制(Controller)
控制用于对程序中的请求进行控制，将用户输入的数据导入模型，充当宏观调控的作用。在Java Web应用程序中，当用户提交HTML表单时，控制层接收请求并调用相应的模型组件去处理，之后调用相应的视图来显示模型返回的数据。在Web引用程序中由Servlet充当  
  
### 模型、视图、控制器的交互关系
1. 首先是展示视图给用户，用户在视图上进行操作，并填写一些业务数据  
2. 然后用户单击提交按钮发出请求  
3. 视图发出的用户请求会到达控制器，请求中包含了想要完成什么样的业务功能及相关的数据  
4. 控制器会处理用户请求，把请求中的数据进行封装，然后选择并调用合适的模型，请模型进行状态更新，然后选择接下来要展示给用户的视图  
5. 模型处理用户请求的业务功能，同时进行模型状态的维护和更新  
6. 当模型状态发生改变的时候，模型会通知相应的视图，告诉视图它的状态发生了改变。  
7. 视图接收到模型的通知后，会向模型进行状态查询，获取需要展示的数据，然后按照视图本身的展示方式，把这些数据展示出来  
  
  
## Model2模型对MVC的实现方法
在Java Web应用开发中，通常把JSP+Servlet+JavaBean的模型称为Model2模型。  
### JSP作为表现层，负责提供页面为用户展示数据，提供相应的表单来用于用户的请求，并在适当的时候(单击提交按钮)向控制器发出请求来请求模型进行更新  
### Servlet作为控制器，用来接收用户提交的请求，然后获取请求中的数据，将之转换为业务模型需要的数据模型，然后调用业务模型相应的业务方法进行更新，同时根据业务执行结果来选择要返回的视图  
### JavaBean作为模型，既可以作为数据模型来封装业务数据，又可以作为业务逻辑模型来包含应用的业务操作。其中，数据模型用来存储或传递业务数据，而业务逻辑模型接收到控制器传过来的模型更新请求后，执行特定的业务逻辑处理，然后返回相应的执行结果  
### Servlet+JSP+JavaBean模型基本的响应顺序是：
当用户发出一个请求后，这个请求会被控制器Servlet接收到；Servlet将请求的数据转换成数据模型JavaBean，然后调用业务逻辑模型JavaBean的方法，并将业务逻辑模型返回的结果放到合适的地方，如请求的属性里；最后根据业务逻辑模型的返回结果，由控制器来选择合适的视图(JSP)，有视图把数据展现给用户  
  
