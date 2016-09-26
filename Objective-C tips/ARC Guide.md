#Objective-C Automatic Reference Counting (ARC)

1. 概述

	**1.1 目的**

	首先，最为重要的目的是创建ARC规范，当编写编译器和运行时代码时必须遵守规范中的语法；
	
	其次，解释为什么ARC要设计成这样，读者应该把注意力集中到该规范的技术设计上，不应该仅仅局限于猜测ARC如何实现；
	
	**1.2 背景**
	
	*文档以下内容最好有一定的C语言基础*
	
	***Blocks***是C语言的一个扩展功能，用于创建匿名函数。通过使用**BlockPointer**，用户可以向Block传递参数并取得Block执行后的返回值。Block可以截获局部变量，截获的变量通过重新初始化到栈（Stack)上；同时，Runtime提供了`block_copy`方法用于copy带有__block标记的变量到堆（heap)上，并设置其ReferenceCount+1（若变量已经在堆上了，仅增加其引用计数）。与之相对的方法`block_release`用于ReferenceCount-1，并销毁在堆上引用计数为0的对象。
	
	Objective-C是一系列编程语言的集合，它是C语言的严格超集。它也是C++的扩展，扩展后的语言称之为Objective-C++，最主要的区别是Objective-C仅支持单重继承。
	
	
	**1.3 演变**
	
	
	
	
2. 摘要

