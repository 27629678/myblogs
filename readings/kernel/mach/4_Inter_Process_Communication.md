Next：[VM接口设计]()，Previous：[Bootstrap]()，Up：[Top]()

## 4 进程间通信（IPC）

这一单介绍`Mach`的`IPC`系统，首先讨论消息的发送和接收，然后再详细描述`端口（Port）系统`。

- [主要概念](./4_Inter_Process_Communication/41_MajorConcepts.md)：有关**Mach端口**的主要概念。
- [消息接口设计]()：主要包含消息的组装，发送和接收。
- [Port接口设计]()：操作端口, 端口权限及端口集合。