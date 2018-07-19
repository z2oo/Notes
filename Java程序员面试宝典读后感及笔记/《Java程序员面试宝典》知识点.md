## 类加载器
### 类加载的一些重要方法
#### loadClass方法
ClassLoader.loadClass()是ClassLoader的入口点，该方法的定义如下：  
` Class loadClass ( String name, boolean resolve) `  
name 是指 JVM 需要的类的名称，resolve 参数告诉方法是否需要解析类。  
> 注意：并不总是需要解析，如果JVM只需要知道该类是否存在或找出该类的超类，那么久不需要解析

#### defineClass方法
defineClass 方法接受由原始字节组成的数组，并把它转换成 Class 对象。
#### findSystemClass方法
findSystemClass 方法从本地文件系统装入文件。它在本地文件系统中寻找类文件，如果存在，就使用 defineClas 将原始字节转换成 Class 对象，以将该文件转换成类。  

当运行 Java 应用程序时，这是 JVM 正常装入类的默认机制。
#### resolveClass方法
是否调用此方法，取决于 loadClass 的 resolve 参数的值。
#### findLoadedClass方法
findLoadedClass 充当一个缓存：当请求 loadClass 装入类时，它调用方法来查看 ClassLoader 是否已经装入这个类，这样可以避免重新装入已存在类所造成的麻烦。
#### findClass方法
loadClass 默认实现调用这个新方法。目的是从本地文件系统使用实现的类装载器装载一个类
#### getSystemClassLoader方法
这个新方法允许 ClassLoader 获取它的父类 ClassLoader。
#### forName方法
类似 loadClass 方法，用来加载类。  

loadClass 加载类实际上就是加载的时候并不对该类进行解析，因此不会初始化该类  

而使用 forName 加载的时候就会将 Class进行解析和初始化  

> 类加载分 3 个阶段：加载、链接、初始化；其中链接又分 3 个阶段：验证、准备、解析。  

> 加载：即将需要用到的 class 文件加载进 内存，将静态数据结构转化成方法区中运行时数据结构，然后生成一个代表该类的 Class 对象存放在堆中（1.7），作为数据访问的入口。  

> 验证：确保加载的类符合 JVM 规范与安全。  

> 准备：为静态变量在方法区中分配空间，设置变量的初始值，这里的初始值不是用户自定义的初始值，而是 0 、false、null、""。

> 解析：主要将常量池中的符号引用转换成直接引用。

> 初始化：初始化阶段是执行类构造器<clinit>()方法。在类构造器中，它将由编译器自动收集类中的所有类变量的赋值动作(准备阶段的a正是被赋值a)和静态变量与静态语句块static{}合并，等待初始化时机。

