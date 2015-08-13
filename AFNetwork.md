#AFNetWork源代码解析


##·AFUrlConnectionOperation

AFUrlConnectionOperation继承自`NSOperation`, 实现了诸如：`NSURLConnectionDelegate`, `NSURLConnectionDataDelegate`, `NSSecureCoding`, `NSCopying`等协议，主要用于实现了`NSUrlConnection`的协议完成整个网络请求，同时该的库的作者还封装了一个Batch接口用于处理多个请求等，还有AF库的线程安全性主要是在该类中通过`NSRecursiveLock`实现的。


###1、__weak & __strong 的使用

```
__weak __typeof(self) weak_self = self;
[xx_manager do_something_in_block:^{
    __strong __typeof(weak_self) strong_self = weak_self;
}];
```

使用`__weak`是为了block不持有self，从而避免循环引用；

在block内使用`__strong`是为了一旦进入block执行，就不允许self在这个执行过程中被释放；block执行完后这个strong_self会自动释放，类似于局部变量从而没有循环引用问题。


###2、NSRecursiveLock

与自旋锁不同，递归锁可以在同一线程多次加锁，只有解锁平衡后（即当前线程没有锁了）其它线程才可以进行加锁/解锁操作；

--------

##·AFURLRequestSerialization

###1、Nullability and Objective-C

从XCode 6.3版本开始，增加了Nullability特性的支持，即增加了两个声明: `__nullable` 和 `__nonnull`。如期待的一样，被__nullable修饰的指针可以有`NULL`或者`nil`的值，相反被`__nonnull`修饰的指针就不可以，若破坏了该规则编译器将给出警告。

```
@property (copy, nullable) NSString *name;
@property (copy, readonly, nonnull) NSArray *allItems;
@property (copy, readonly) NSArray * __nonnull allItems;

- (nullable AAPLListItem *)itemWithName:(nonnull NSString *)name;
```
