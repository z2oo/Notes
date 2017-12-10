# 如何在Java中实现线程
在语言层面有两种方式：

1. java.lang.Thread类的实例就是一个线程，但是它需要调用java.lang.Runnable接口来执行
2. 由于线程类本身就是调用Runnable接口，所以你可以继承java.lang.Thread类或者直接调用Runnable接口来重写run()方法来实现线程  
