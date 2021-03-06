# Java内存模型是什么
Java内存模型规定和指引Java程序在不同的内存架构、CPU和操作系统间有确定性的行为  

它在多线程的情况下尤其重要  

Java内存模型对一个线程所做的变动能被其他线程可见提供了保证，它们之间是先行发生关系  

这个先行发生关系定义了一些规则让程序员在并发编程时思路更清晰  

比如，线性发生关系确保了：
- 线程内的代码都能够按先后顺序执行，这被称为程序次序规则

- 对用同一个锁，一个解锁操作一定要发生在时间上后发生的另一个锁定操作之前，也叫做管程搜定规则
- 前一个对volatile的写操作在后一个volatile的读操作之前，也叫volatile变量规则
- 一个线程内的任何操作必须在这个线程的start()调用之后，也叫作线程启动规则
- 一个线程的所有操作都会在线程终止之前，线程终止规则
- 一个对象的终结操作必须在这个对象构造完成之后，页脚对象终结规则
- 可传递性

推荐书籍：Java并发编程实践