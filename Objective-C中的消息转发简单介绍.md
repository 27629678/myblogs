###objective-c中的消息转发

####一、消息转发

```
[obj message];
```

以上代码段就是一个简单的消息转发的实例，废话不多说；

####二、消息转发中几个重要的运行时方法

方案一:

1. + (BOOL)resolveClassMethod:(SEL)aSelector;
2. + (BOOL)resolveInstanceMethod:(SEL)aSelector;

方案二:

3. - (id)forwardingTargetForSelector:(SEL)aSelector;

方案四:

1. - (void)forwardInvocation:(NSInvocation *)invocation;
2. - (NSMethodSignature *)methodsignatureForSelector:(SEL)aSelector;

**方法注释就不做过多解释，主要讲解一下作用和执行顺序：**

- 作用：以上三个方案都用来实现消息转发；
- 执行顺序：以上三套方案代码同时存在时，代码优先执行顺序为：方案一 -> 方案二 -> 方案三；