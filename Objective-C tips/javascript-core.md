## JavascriptCore基本使用

### 0x01 JSContext

**JSContext**提供了一个`Javascript`的执行环境，与DOM对象中的Window一样；

### 0x02 JSValue

提供了`Javascript`与`Obj-C/Swift`数据类型的转换，并绑定到**JSContext**对象上；

- \- (BOOL)toBool;
- \- (double)toDouble;
- \- (int32_t)toInt32;
- \- (uint32_t)toUInt32;
- \- (NSNumber \*)toNumber;
- \- (NSString \*)toString;
- \- (NSDate \*)toDate;
- \- (NSArray \*)toArray;
- \- (NSDictionary \*)toDictionary;
- \- (id)toObject;
- \- (id)toObjectOfClass:(Class)expectedClass;

```
NSString* factorialScript = loadFactorialScript();
[_context evaluateScript: factorialScript];
JSValue *function = _context[@"factorial"];
JSValue *result = [function callWithArguments:@[@5]];
NSLog(@"factorial(5) = %d", [result toInt32]);
```

### 0x03 Javascript -> Objective-C

JS调用Obj-C主要有两种方式：

- Blocks
- JSExport Protocol

#### 3.1 Blocks

```
_context[@"makeColor"] = ^(NSDictionary *rgb) {
	float r = rgb[@"red"].floatValue;
	float g = rgb[@"green"].floatValue;
	float b = rgb[@"blue:].floatValue;
	
	return [NSColor colorWithRed:(r/255.f) green: (g/255.f) blue:(b/255.f) alpha:1.f];
};

```

#### 3.2 Caveats:

- 避免捕获**JSValue**对象；
	1. 倾向传递参数
	
- 避免捕获**JSContext**对象；
	1. 使用`+ [JSContext currentContext]`方法；
	
#### 3.2 JSExport

```
//@interface MyPoint
@protocol MyPointExports <JSExport>

@property double x;
@property double y;

- (NSString *)description;

+ (MyPoint *)makePointWithX:(double)x y:(double)y;

@end

@interface MyPoint: NSObject <MyPointExports>

- (void)myPrivateMethod;	// not visible to Javascript code

@end

```

### 0x04 内存管理

- Obj-C使用ARC；
- Javascript使用垃圾回收机制；

所以，所有的内存对象都强引用的，避免内存对象循环引用问题；

```
@implementation MyButton

- (void)setOnClickHandler:(JSValue *)handler
{
	// weak reference to a javascript value
	_onClickHandler = [JSManagedValue managedValueWithValue:handler];
	
	// turn JSManagedValue into a garbage collected reference
	[_context.virtualMachine addManagedReference:_onClickHandler withOwner:self];
}

@end
```

### 0x05 JSVirtualMachine（线程安全的）

一个（或多个）**JSContext**的容器，在一个虚拟机内，**JSContext**之间可以传递数据，但是在多个虚拟机之间不可以交换数据；这是因为，每个虚拟拥有一个内存垃圾回收导致的；

