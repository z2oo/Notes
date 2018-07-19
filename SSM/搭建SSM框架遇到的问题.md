
有些时候在web.xml文件中，我们通常都需要配置 DispatcherServlet 前置控制器来进行对 URL 的拦截，并将其在 web 容器启动时就初始化

但有时候我们既可以配置 ApplicationContext 和 ContextLoaderListener，用它们两个来进行加载对 Spring 通用的配置，也可以不配置它们，而只配置 DispatcherServlet 。  


为什么呢?

---
因为配置 DispatcherServlet 其实就是配置 SpringMVC 容器，而 SpringMVC 容器跟 Spring 容器是父子容器关系。父子关系中，子容器可以访问父容器的内容，而父容器没有权利访问子容器的内容。
