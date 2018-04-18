## public vs open

**Open**的访问级别最宽泛，**Public**次之；

它们的区别在`模块`之间的访问控制，不同之处如下：

- **Public**类不可以在其它`模块`中被用作基类进行派生自定义类型；
- **Public**成员不可以在其它`模块`中被重写；

以下内容摘自[Apple官方文档](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AccessControl.html)

> Open access applies only to classes and class members, and it differs from public access as follows:

> Classes with public access, or any more restrictive access level, can be subclassed only within the module where they’re defined.

> Class members with public access, or any more restrictive access level, can be overridden by subclasses only within the module where they’re defined.

> Open classes can be subclassed within the module where they’re defined, and within any module that imports the module where they’re defined.

> Open class members can be overridden by subclasses within the module where they’re defined, and within any module that imports the module where they’re defined.

> Marking a class as open explicitly indicates that you’ve considered the impact of code from other modules using that class as a superclass, and that you’ve designed your class’s code accordingly.

