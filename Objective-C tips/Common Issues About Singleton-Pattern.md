####0、线程安全

**线程安全问题是由全局变量或者静态变量引起的**

####1、同步机制

1. 锁，时间换空间，常见使用Double-Checked Locking, 不多啰嗦；
2. ThreadLocal（Spring中引入），用于解决同步问题引入新的机制，空间换时间；为每个线程提供一份独立的变量副本，从而隔离了多个线程对数据的访问冲突，提高并发的性能；

####2、注意事项

1. 写公用类时，要对多线程调用情况备注明确说明；
2. 对每一个共享可变变量都要注意线程安全性；
3. 类和方法做设计时，尽量设计成无状态的；