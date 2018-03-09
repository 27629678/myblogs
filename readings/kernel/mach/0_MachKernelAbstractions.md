## Mach内核抽象

Mach提供了简单并功能强大的一系列抽象，主要的抽象如下：

- Tasks，拥有资源的单元；每个Task由虚拟内存空间（virtual address space），端口权限命名空间（port right namespace），一个（或多个）线程组成；
- Threads，一个Task中可在CPU上运行的单元，其资源由其父Task提供；
- Address space，利用`内存管理器（memory managers）`，Mach实现了`虚拟内存`和`共享内存`的概念；
- Memory objects，内存管理的内部存储单元，包含命名入口（entries）和内存区域（regions）。
- Ports，安全、简单的通信通道，仅通过`端口权限`进行发送和接口操作；
- IPC，消息队列（MQ），远程过程调用（RPC），通知（Notifications），信号量（Semaphores）和锁（Lock Sets）；

> **NOTE：**
> 
> **MacOS X的进程**是基于Mach Tasks实现的；
> 
> **POSIX线程**是基于Mach Threads实现的；
> 
> 请注意，Task没有生命的，只有Thread才能执行指令；若Task没有Thread，是不可以运行的，即创建一个进程后，通常会包含一个主线程；