# Runnable和Callable有什么不同
Runnable和Callable都代表那些要在不同的线程中执行的任务  

Runnable从JDK1.0就有了，Callable是在JDK1.5增加的  

它们的主要区别是Callable的call()方法可以返回值和抛出异常，而Runnable的run()方法没有这些功能  

Callable可以返回装载有计算结果的Future对象  
