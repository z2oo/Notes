# AOP
AOP ( Aspect Orient Programming ),也就是面向切面编程  

AOP 与 OOP 互为补充，面向对象编程将程序分解成哥哥层次的对象，而面向切面编程将程序运行过程分解成各个切面  

可以这样理解：
- 面向对象编程是从静态角度考虑程序结构  
- 而面向切面编程是从动态角度考虑程序运行过程  
  
---

AOP 分为两类：
- 静态AOP实现：AOP 框架在编译阶段对程序进行修改，即实现对目标类的增强，生成静态的 AOP 代理类 ( 生成的 *.class 文件已经被改掉，需要使用特定的编译器 )。以 AspectJ 为代表
- 动态AOP实现：AOP 框架在运行阶段动态生成 AOP 代理 ( 在内存中以 JDK 动态代理或 cglib 动态地生成 AOP 代理类 )，以实现对目标对象的增强。以 Spring AOP 为代表  


