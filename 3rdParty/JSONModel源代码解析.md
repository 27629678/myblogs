#JSONModel源代码解析

###- NSDecimalNumber

`NSDecimalNumber`是`NSNumber`的子类，是不可变的对象，包装了以10为底数的数学运算。任何一个数字实例可以用尾数（mantissa）*10^指数（exponent）来表示，其中，尾数的精度为38位长的十进制数，指数的取值范围为（-128-127）的整数。

```
/**
 *  实例化一个|NSDecimalNumber *|对象
 *
 *  @param mantissa 尾数
 *  @param exponent 指数
 *  @param flag     正负数标记
 *
 *  @return <#return value description#>
 */
NSDecimalNumber* dcm = [NSDecimalNumber initWithMantissa:(unsigned long long)mantissa exponent:(short)exponent isNegative:(BOOL)flag];
```

###- `+ (void)initialize`与`+ (void)load`的区别

- initialize:初始化类在该类收到第一条调用消息，线程安全且调用顺序是super class -> sub class; 父类中的initialize实现可能会被调用多次，比如子类没有实现该方法或者子类显式地调用父类的实现`[super initialize]`。

若要规避实现代码被执行多次可以使用以下代码段：
```
+ (void)initialize
{
	if (self == [ClassName self]) {
		// do initialization
		...
	}
}
```

> Note:`initialize`只被执行一次，若要根据不同的类执行不同的代码可以使用`load`方法

- load:该方法当任何一个类被添加到运行时系统时被执行一次；

###- class_copyPropertyList(Class cls, unsigned int *count)

用于获取类下所有被声明的属性

###- property_getName(objc_property_t)

用于获取被声明属性的名字

###- property_getAttributes(objc_property_t)

用于获取属性名字的描述性的属性

**更多详细介绍请查看官方文档**
