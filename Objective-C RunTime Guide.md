##Interacting with the runtime

----

####Legacy and Modern Versions

以Ojbjective-C语言的2.0版本为分界线，以前是Legacy Version, 2.0以后为Modern Version

####Platforms

iPhone和OS X v10.5以后的64位应用程序使用Modern Version, 其它的应用程序使用的Legacy Version

####NSObject Methods

1. description:子类可以重写该方法，用于调试时显示更详细的关于该实例的信息
2. 用于查询runtime system信息的方法，如下：
	- class 标识自己是什么类（Class）
	- isKindOfClass:|isMemberOfClass:测试类在继承层级上的位置
	- respondsToSelector:测试实例是否可以接收特定的消息
	- conformsToProtocol:测试实例是否实现了特定的协议
	- methodForSelector:提供方法IMP的地址

####The objc_msgSend Function

***为了加速消息的处理，runtime system缓存了已经处理过的消息的Selector和Imp的地址，每个类都有单独的缓存；在开始搜索类的DispatchTalbe之前，首先检查缓存；***

动态绑定语言的不足之处：

- 若消息转发时，消息已经在缓存中，此时仅仅比`方法调用`慢一点点；
- 若消息转发时，消息没有在缓存中，此时要去`DispatchTable`中查找，此时会略慢一些；
- 当程序运行一段时间后，几乎所有的消息可以在缓存中定位到（**基于一个方法被调用后还会继续使用的原理**）

规避动态绑定的方法就是直接使用程序实现的内存地址传递参数达到直接调用的目的，常用的环境如批量的for循环，方法如下：

```
void (*setter)(id, SEL, BOOL);
int i;

setter = (void(*)(id, SEL, BOOL))[target methodForSelector:@selector(setEnable:)];

for (int i = 0; i < 1000; i ++) {
	setter(target[i], @selector(setEnable:), YES);
}
```

####Dynamic Method Resolution

某些环境下，你可以想要动态的提供一个方法的实现部分，可以声明属性:

```
@dynamic propertyName
```

该方法用于告诉编译器，与该属性相关的方法会动态的提供；

并实现`resolveInstanceMethod:`或者`resolveClassMethod:`方法用来完成向类中添加动态实现的方法；

OC方法与C语言类似，带有两个隐藏的参数`self`和`_cmd`，并使用`class_addMethod`方法向类中添加新方法。

```
void dynamicMethodIMP(id self, SEL _cmd) {
	// implementations
}
```

添加方法：

```
@implementation MyClass

+ (BOOL)resolveInstanceMethod:(SEL)aSEL
{
	if (aSEL == @selector(resolveThisMethodDynamically)) {
		class_addMethod([self class], aSEL, (IMP)dynamicMethodIMP, "v@:");
		return YES;
	}
	
	return [super resolveInstanceMethod];
}
```

####Forward Messages

使用`forwardInvocation:`转发消息，务必做到以下两点：

- 是否确定该消息响应自定义方法
- 实现原始消息转发

使用`invokeWithTarget:`方法转发消息的代码如下：

```
- (void)forwardInvocation:(NSInvocation *)anInvocation {
	if ([someOhterObject responseToSelector:[anInvocation selector]]) {
		[anInvocation invokeWithTarget:someOtherObject];
	}
	else {
		[super forwardInvocation:anInvocation];
	}
}
```

可以使用该方法来模拟多重继承，如下图所示：

![figure-mutiple_inheritance](/Users/H-YXH/Documents/markdown/wordpress/objective-c_runtime_guide_mutipule-inheritance.png)

-----

***note that***

`[aWarrior respondsToSelector:@selector(negotiate)]`的返回值是NO，尽管可以给`aWarrior`发送`negotiate`消息。

-----

大多时候，返回值为NO是正确的答案；但是，未必是绝对的，假如使用上述方案，返回值就不能正确反应真正的问题。

此时，要重写`respondsToSelector:`和`isKindOfClass:`方法，如下所示：

```
- (BOOL)respondsToSelector:(SEL)aSelector {
	if (super responseToSelector:aSelector]) {
		return YES;
	}
	else {
		/* Here, test whether the selector message can be
		 * forward to another object and whether that object
		 * can response to it. Return YES if it can.
		 * */
	}
}
```

**One More Thing**，若使用了协议，要重写`conformsToProtocol:`方法；若一个对象可以转发任何它收到的消息，该对象必须实现`methodSignatureForSelector:`方法，代码如下所示：

```
- (NSMethodSignature*)methodSignatureForSelector:(SEL)selector {
	NSMethodSignature* signature = [super methodSignatureForSelector:selector];
	if (!signature) {
		signature = [surrogate methodSignatureForSelector:selector];
	}
	
	return signature;
}
```