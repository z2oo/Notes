下面不属于HttpServletRequest接口完成功能的是？  

正确答案: C   你的答案: A

A.读取cookie  
B.读取HTTP头  
C.设定响应的content类型  
D.读取路径信息  
<hr/>
A.request.getCookies();
B.request.getHeader(String s);
C.response.setContentType("text/html;charset=utf-8");
D.request.getContextPath();/request.getServletPath();
<hr/>
HttpServletRequest类主要处理：
1. 读取和写入HTTP头  
2. 取得和设置cookies(response是addCookie())
3. 取得路径信息
4. 标识HTTP会话  
<hr/>
HttpServletRequest接口最常用的方法就是获得请求中的参数，这些参数一般是客户端表单中的数据。同时HttpServletRequest接口可以获取由客户端传送的名称，也可以获取产生请求并且接收请求的服务器端主机名及IP地址，还可以获取客户端正在使用的通信协议等信息