# 基于AOP+NSProxy的网络流量统计及性能监控组件的实现

**标签：**`Runtime`，`AOP`，`消息转发`，`网络流量监控`

首先我们先介绍一下基本术语，什么是AOP及NSProxy的用途，最后再简单介绍一下如何通过这两个东西实现网络流量及性能监控的实现；

### 0x01 AOP介绍

AOP（切面编程，Aspect Oriented Programming的缩写），实现DI（信赖注入）的框架或者是工具，此处不做过多的展开介绍，可参考以下不同语言的实现：

- [[Google|Guice]](https://github.com/google/guice)
- [[Facebook|Fishhook]](https://github.com/facebook/fishhook)
- [[Aspects]](https://github.com/steipete/Aspects)，基于Objective-C的实现，有兴趣的同学可以研究一下其它实现方法；
- 其它

### 0x02 NSProxy介绍

与`NSObject`类（class）一样，另外一个实现`<NSObject>`协议的基类，与`NSObject`基类不同之处，`NSProxy`是一个抽象基类，没有默认的`initializer`方法，需要子类去实现；并且在子类中必须实现以下两个方法：

- \- (void)forwardInvocation:(NSInvocation *)invocation;
- \- (nullable NSMethodSignature *)methodSignatureForSelector:(SEL)sel;

> NOTE: forwardInvocation:方法是发挥想像力的地方，不但可以执行Native实例的方法，还可以执行RPC调用等高级玩法；

在这里，我也不免俗，上一些代码看一下与NSObject基类在调用NSObject协议方法时有哪些不同，上面已经提到了`NSProxy`是一个抽象基类，此处不再赘述；主要看以下几个协议方法：

```
- (BOOL)isProxy;

- (BOOL)isKindOfClass:(Class)aClass;
- (BOOL)isMemberOfClass:(Class)aClass;
- (BOOL)conformsToProtocol:(Protocol *)aProtocol;

- (BOOL)respondsToSelector:(SEL)aSelector;
```

测试代码：

```
// NEProxy.m
@interface NEProxy ()

@property (nonatomic, weak) id target;
@property (nonatomic, weak) id delegate;

@end

@implementation NEProxy

+ (instancetype)proxyWithTarget:(id)target delegate:(id)delegate
{
    NEProxy *proxy = [NEProxy alloc];
    
    proxy.target = target;
    proxy.delegate = delegate;
    
    return proxy;
}

- (void)forwardInvocation:(NSInvocation *)invocation
{
    if ([self.target respondsToSelector:invocation.selector]) {
        [invocation invokeWithTarget:self.target];
    }
    
    if ([self.delegate respondsToSelector:invocation.selector]) {
        [invocation invokeWithTarget:self.delegate];
    }
}

- (NSMethodSignature *)methodSignatureForSelector:(SEL)sel
{
    NSMethodSignature *signature = [self.target methodSignatureForSelector:sel];
    
    // similar to the method: void * isEnabled(void) {}
//    signature = [NSMethodSignature signatureWithObjCTypes:"@@:"];
    
    return signature;
}

@end

// main.m

int main() {
		NSString *greeting = @"hello, world."; // length = 13
		NEProxy *proxy = [NEProxy proxyWithTarget:greeting delegate:nil];
		
		assert([proxy isKindOfClass:[NSString class]]);
//	  	assert([proxy isMemberOfClass:[NSString class]]);
		assert([proxy conformsToProtocol:@protocol(NSCopying)]);
		assert([proxy respondsToSelector:@selector(length)]);
		
		NSNumber *length = @([(NSString *)proxy length]);
		length = [(NSObject *)proxy valueForKey:@"length"];
		
		if ([proxy isEqual:greeting]) {
				// not reached
		}
		
		return 0;
}
```

做一个简单的分析，以上代码编译+运行没有问题，也是按预期执行的；

#### 2.1 