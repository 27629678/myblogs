###Dispatch_Once

都知道dispatch_once的用法，代码如下：

```
- (instancetype)sharedInstance
{
	static NSObject* instance = nil;
	static dispatch_once_t onceToken;
	dispatch_once(&onceToken, ^{
		instance = [NSObject alloc] init];
	});

	return instance;
}
```

dispatch_once的优点：

- 代码只执行一次；
- 线程安全；
- 代码简洁；

稍有不甚，这个东西是个巨坑：

- onceToken没有指定static;
- onceToken被赋值（非0值）；
- 一个类内部方法的实例方法使用该代码块导致对象输出结果异常等等；

下面，依次解释：

`dispatch_once()`宏展开后，代码如下：

```
void
_dispatch_once(dispatch_once_t *predicate, dispatch_block_t block)
{
    if (DISPATCH_EXPECT(*predicate, ~0l) != ~0l) {
        dispatch_once(predicate, block);
    }
}
```

`DISPATCH_EXPECT`宏展开后，代码如下：

```
#if __GNUC__
#define DISPATCH_EXPECT(x, v) __builtin_expect((x), (v))
#else
#define DISPATCH_EXPECT(x, v) (x)
#endif
```

按照以上宏展开后的代码，我们可以想象一下，其结果伪代码如下：

```
- (instancetype)sharedInstance
{
	static NSObject* instance = nil;
	static dispatch_once_t onceToken = 0;
	if (onceToken != 0)
	1...	// 具体做什么, 未知
	{
		instance = [NSObject alloc] init];
	}
	2...	// 改变onceToken的值为~0l, (long 0 取反)
	
	return instance;
}
```

到此为止，我们就可以解释上面留下来的三个巨坑：

1. 没有指定static的后果，因为没有指定static，C block没有执行前，onceToken的生命周期已经结束，当C block执行时该变量的内存地址指向的区域已经被回收或另做它用，此时再去访问会造成内存访问错误，程序直接Crash掉；
2. onceToken被赋值为非0值，每次调用sharedInstance方法时都会重新给该onceToken赋值，导致无法满足进入安全并且仅执行一次的代码块，导致意想不到的后果，若是方法为实例化单例，会直接导致该对象永远无法正常实例化；
3. onceToken被赋值为0值，由第二条可知，每次调用sharedInstance方法时与调用[NSObject alloc] init]没有两样，最终导致无法做到单例的效果；
4. 若使用dispatch_once来达到类内部实例方法实现只执行一次的效果，由于onceToken是类内部static的变量，第二次及后续实例化的对象执行该方法时没有修改onceToken的值，导致程序的执行结果无法预想；

> **Note:**快速安全书写dispatch_once的宏

```
/**************************************************************/
// 执行一次
#undef  XY_ONCE_BEGIN
#define XY_ONCE_BEGIN( __name ) \
static dispatch_once_t once_##__name; \
dispatch_once( &once_##__name , ^{

#undef  XY_ONCE_END
#define XY_ONCE_END     });
简化后的写法
- (IBAction)clickOnce:(id)sender {
    XY_ONCE_BEGIN(a)
    SHOWMBProgressHUD(@"only show once", nil, nil, NO, 2)
    XY_ONCE_END
}
```