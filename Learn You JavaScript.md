###1、自执行函数

代码形式如下：

```
(function() { code here })()
```

\- 优点：pass

\- 缺点：pass


###2、内建对象**arguments**

js的方法有一个叫作**arguments**的内建的对象，该对象用于保存方法被调用（called or invoked）时传递进来的参数。

```
function js_log() {
	var items = Array.prototype.slice.call(arguments)
	console.log(items)
}

(function() {
	js_log('1','2')
})()
```

执行以上脚本输出如下信息：

```
[ '1', '2' ]
```

###3、全局对象

在代码的最顶级——不在任何函数内的JavaScript代码内，可以使用JavaScript关键字this来引用全局对象

```
var global = this;	// 定义一个引用全局对象的全局变量
```

>NOTE:在客户端JavaScript中，在其表示的浏览器窗口中的所有JavaScript代码中，Window对象充当了全局对象。

###4、相等和不等运算符

`=`：赋值

`==`：相等，用来检测两个操作数是否相等，允许进行类型转换

`===`：恒等，用来检测两个操作数是否严格相等，即值与类型是否相等

严格相等运算符`===`首先计算其操作数的值，比较过程中没有任何类型变换：

- 如果两个值类型不相同，则它们不相等
- 如果两个值都是`null`或`undefined`，则它们不相等
- 如果两个值都是`true`或`false`，则它们相等
- 如果其中一个值是`NaN`或两个值都是`NaN`，则它们不相等。`NaN`和其它任何值都不相等，包括它本身。可以通过`x!==x`来u判断x是否为`NaN`，只有x的值为`NaN`时，这个表达式的值才为`true`
- 如果两个值为数字且相等，则它们相等
- 如果两个值为字符串且所含的对应位上的16位数完全相等，则它们相等
- 如果两个引用值指向同一个对象/数组/函数，则它们相等。如果指向不同的对象，则它们不相等，尽管两个对象具有完全一样的属性

###5、对象

除了`字符串`、`数字`、`true`、`false`、`null`和`undefined`之外，JavaScript中的值都是**对象**。`属性`包括名字和值，属性名可以是包括空字符串在内的任意字符串，但**对象**中不能存在两个同名的属性，值可以是任意JavaScript值。

####-5.1 原型

每个JavaScript对象（null除外）都和另一个对象关联，“另一个”对象就是我们熟知的**原型**，每个对象都是从**原型**继承属性。

####-5.2 删除属性

`delete`运算符只能删除**自有**属性，不能删除**继承**属性（要删除继承属性必须从定义这个属性的原型对象上删除，而且这会影响到所有继承自这个原型的对象）。

####-5.3 检测属性

可以通过`in运算符`、`hasOwnProperty()`和`propertyIsEnumerable()`三个方法来完成检测属性的工作。

```
var o = {x:1}
"x" in o;			// true, "x"是o的属性
"y" in o;			// false, "y"不是o的属性
"toString" in o;	// true, o继承自toString属性
```

`hasProperty()`方法用来检测给定的名字是否是对象的自有属性，对于继承属性它返回false。

```
var o = {x:1}
o.hasProperty("x");			// true, "x"是o的属性
o.hasProperty("y");			// false, "y"不是o的属性
o.hasProperty("toString");	// false, toString是继承属性
```

`propertyIsEnumerable()`是`hasProperty()`的增强版，只有检测是自有属性且这个属性是**可枚举（enumerable attribute）**时为true，且某些内置属性是不可枚举的，例如toString属性。

```
var o = inherit({x:1})
0.x = 1;
o.propertyIsEnumerable("x");			// true, o有一个可枚举自有属性
o.propertyIsEnumerable("y");			// false, y是继承属性
Object.prototype.propertyIsEnumerable("toString"); // false, 不可枚举
```

####-5.4、属性的特性

对于库开发者来讲，设置属性特性的API是非常重要的，因为：

- 可以通过API给原型对象添加方法，并将它们设置成不可枚举的，这让它们看起来更像内置方法
- 可能通过API给对象定义不能修改或删除的属性，借此“锁定”这个对象

数据属性的4个特性分别是：

- 值（value）
- 可写性（writable）
- 可枚举性（enumerable）
- 可配置性（configurable）

可以通过`Object.getOwnPropertyDescriptor()`获得对象特定属性的描述符：

```
// return {value:1, writable:true, enumerable:true, configurable:true}
Object.getOwnPropertyDescriptor({x:1}, "x")
```

可以通过`Object.defineProperty()`传入要修改的对象，要创建的属性的名称及属性描述符：

```
var o = {}	// create a new object

// add a unenumerable property
Object.defineProperty(o, "x", {value:1, writable:true,enumerable:false,configurable:true})

// 属性存在，但不可枚举
o.x					// => 1
Object.keys[o]	// => []

// 现在对属性x做修改，让它只读
Object.defineProperty(o, "x", {writable:false})

o.x = 2			// 操作失败但不报错，而在严格模式中抛出类型错误异常
o.x					// => 1

// 属性依然是可配置的，因此可以通过这种方式修改
Object.defineProperty(o, "x", {value:2})

// 现在将x从数据属性修改存取器属性
Object.defineProperty(o, "x", {get:function () {return 0;})

o.x 				// => 0

```

####-5.5、序列化对象

对象序列化（serialization）是指将对象的状态转换为字符串，也可将字符串还原为对象。

提供了`JSON.stringify()`和`JSON.parse()`用来序列化和还原JavaScript对象，JSON的全称是Java Script Object Notation。

```
o = {x:1, y:{z:[false, null, ""]}}		// 定义一个测试对象
s = JSON.stringify(0)						// s是'{"x":1,"y":{"z":[false, null, ""]}}'
p = JSON.parse(s)							// p是o的深拷贝
```

###6、函数

- subroutine:子例程
- procedure:过程
- parameter:形参
- argument:实参
- context:上下文，函数作为对象的属性称为对象的方法，此时这个对象就称为函数的上下文
- constructor:构造函数
- closure:闭包，定义在函数中并可以使用作用域中的任何变量，此时的函数构成了闭包

>NOTE：参数有形参（parameter）和实参（argument）的区别，形参相当函数中定义的变量，实参是在运行时的函数调用时传入的参数。

####- 6.1、函数全名

当函数名包含多个单词时，一种约定是将单词以下划线分隔，就像like_this()。还有另外一种约定，就是除了第一个单词首字母使用大写字母，就像LikeThis()。有一些函数是用作内部函数或私有函数通常以一条下划线为前缀。

####- 6.2、函数调用

- 作为函数
- 作为方法
- 作为构造函数
- 通过它们的call()和apply()方法间接调用

>NOTE:JavaScript中的函数也是**对象**，和其它JavaScript对象没有什么两样，函数对象也可以包含方法，即call()和apply()两个间接调用方法。

####- 6.3、可变长参数列表

在函数体内，标识符**arguments**是指向实参对象的引用，实参对象是一个类数组对象。

```
function f(x,y,z)
{
	// verify arguments length
	if (arguments.length != 3) {
		// print error
	}
	
	...
}
```

>NOTE:大多数情况下，JavaScript的默认行为是省略的实参是**undefined**,多出的参数会自动省略的

```
// varargs function
function max(/* ... */)
{
	var max = Number.NEGATIVE_INFINITY
	
	for (var i = 0; i < arguments.length; i ++)
	{
		if (max < arguments[i]) max = arguments[i]
	}
	
	return max
}
```

###13、浏览器中的JavaScript

####13.3 JavaScript程序的执行

客户端JavaScript程序没有严格的定义，所有这些单独的代码共用同一个全局`Window`对象，这意味着它们都可以看到相同的`Document`对象，可以共享相同的全局函数和变量的集合：如果一个脚本定义了新的全局变量和函数，那么这个变量或函数会在脚本执行之后对任意的JavaScript代码可见。

如果Web页面包含一个嵌入的窗体（通常使用<iframe>元素），嵌入文档中的JavaScript代码和被嵌入文档里的JavaScript代码会有不同的全局对象，它可以当作一个单独的JavaScript程序。
