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

#### 3.1 变量声明提升（hoisting）

```
console.log(variable);		// undefined, default
var variable = 1;
console.log(variable);		// 1
```

> **NOTE:**与`函数提升`不同

```
// 函数声明
foo();			// bar

function foo() {
	console.log('bar');
}

// 函数表达式，不可以提升
foo1();		// TypeError: foo1 is not a function
// 此时的foo1是一个变量（声明提升，类型为undefined），
// 因此，foo1()不是一个函数

var foo1 = function() {
	console.log('bar1');
}
```

> **NOTE:**表达式定义的函数，成为`匿名函数`,匿名函数没有函数提升;

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

除了`字符串`、`数字`、`boolean`、`null`和`undefined`之外, Symbols(ES6引入的新类型)，JavaScript中的值都是**对象**。`属性`包括名字和值，属性名可以是包括空字符串在内的任意字符串，但**对象**中不能存在两个同名的属性，值可以是任意JavaScript值。

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

如果Web页面包含一个嵌入的窗体（通常使用\<iframe\J>元素），嵌入文档中的JavaScript代码和被嵌入文档里的JavaScript代码会有不同的全局对象，它可以当作一个单独的JavaScript程序。

###14、Window对象

####14.1 Timer

如果以0毫秒的超时时间来调用`setTimeOut()`，那么指定的函数不会立即执行。相反，会把它入到队列中，等到前面处于等待状态的事件处理程序全部执行完成后，再“立即”调用它。

####14.2 Location

Document对象的location属性也引用到Location对象；

```
window.location === document.location	// 总是返回true
```

Document对象也有一个URL属性，是文档首次载入后保存该文档的URL的静态字符串。如果定位到文档中的片段标识符（如#table-of-contents），location对象会做相反的更新，而document.URL属性却不会改变。

#####14.2.1 Parser URL

Location对象的hash和search属性比较有趣。如果有的话，hash属性的返回URL中的**片段标识符**部分；search属性也类似，返回的是问号之后的URL，这部分通常是某种类型的查询字符串。

#####14.2.2 Load New Document

Location对象的`assign()`方法可以使窗口输入并显示你指定的URL中的文档。`replace()`方法也类似，但是它在载入新的文档之前会删除浏览历史中的当前文档。

如果脚本无条件地载入一个新的文档，`replace()`方法可能比`assign()`方法更好的选择。

`#top`标识符是个特殊的例子：如果文档中没有元素的ID是“top”，它会让浏览器跳到文档的开始处。

```
location = "#top";		// 跳转到文档的顶部
```

####14.3 History

出于安全考虑，脚本无法访问已保存的URL历史。

History对象的`back()`和`forward()`方法与浏览器的“后退”和“前进”按钮一样：它们使浏览器在浏览历史中前后跳转一格。第三个方法——`go()`方法接受一个整数参数，可以在历史列表中向前（正参数）或向后（负参数）跳过任意多个页。

####14.4 Navigator

navigator对象：

- appName:Web浏览器的全称；
- appVersion:此属性通常以数字开始；
- userAgent:浏览器在它的USER-AGENT HTTP头部中发送的字符串；
- platform:在其上运行浏览器的操作系统的字符串；
- onLine:浏览器当前是否连接到网络；
- geolocation:确定用户地理位置信息的接口；
- javaEnabled():一个非标准的方法，当浏览器可以运行java小程序时返回true；
- cookieEnable():非标准方法，如果浏览器可以存储永久的cookie时，返回true。当cookie配置为“视具体情况而定”时可能返回不正确的值；

####14.8 Window的打开与关闭

- window.open():四个参数略；
- window.close():关闭一个窗口；

### 15. Class

`JavaScript`是基于`原型(prototype)`的程序设计语言，与面向对象的编程的风格不同，但是它也是可以进行代码重用的，比如：继承、封装和多态等；

在**ES6**之前，**类(Class)**的声明如代码所示：

```
function Person(name) {
	this.name = name;
}

// add instance method
Person.prototype.sayHello = function () {
	console.log('Hello, I\'m ' + this.name + '.');
}

var p1 = new Person('xiaohong');
p1.sayHello();	// output: Hello, I'm xiaohong.

// 这里有个坑
var sayHello = p1.sayHello;

sayHello();		// output: Hello, I'm undefined.

sayHello.call(p1);	// output: Hello, I'm xiaohong.
```

在**ES6**之前，**类(Class)**的继承如代码所示：

```
// Student: Person
function Student(name, number) {
	Person.call(this, name);
	this.number = number;
}

// 到这里还没有完，需要改造原型
// 1. 基于Person的原型
Student.prototype = Object.create(Person.prototype);

// 2. 修改其构造函数，指向Student
Student.prototype.constructor = Student;

// 3. 替换sayHello方法
Student.prototype.sayHello = function(){
  console.log("Hello, I'm " + this.firstName + ". My number is: " + this.number + ".");
};

var s1 = new Student('hzyuxiaohua', 2204);

s1.sayHello()		// output: Hello, I'm hzyuxiaohua. My number is: 2204.
```

#### 15.1 Non-Hoisted

```
var obj = new MyClass();	// ReferenceError

class MyClass{}
```

#### 15.2 constructor, static, prototype methods

ES6 新增了class 关键字，但只是语法糖，JavaScript 仍旧是基于原型，如下代码所示：

```
class Foo {
	constructor (prop) {
		this.prop = prop;
	}
	
	static staticMethod () {
		return 'static method';
	}
	
	prototypeMethod () {
		return 'prototype method';
	}
	
	['computed' + 'method']() {
		return 'computed method';
	}
}
```

#### 15.2 命名空间

命名空间是一个容器，它允许开发人员在一个独特的，特定于应用程序的名称下捆绑所有的功能。 **在JavaScript中，命名空间只是另一个包含方法，属性，对象的对象。**

```
// 全局命名空间
var MYAPP = MYAPP || {};

// 子命名空间
MYAPP.event = {};

// 给普通方法和属性创建一个叫做MYAPP.commonMethod的容器
MYAPP.commonMethod = {
  regExForName: "", // 定义名字的正则验证
  regExForPhone: "", // 定义电话的正则验证
  validateName: function(name){
    // 对名字name做些操作，你可以通过使用“this.regExForname”
    // 访问regExForName变量
  },
 
  validatePhoneNo: function(phoneNo){
    // 对电话号码做操作
  }
}

// 对象和方法一起申明
MYAPP.event = {
    addListener: function(el, type, fn) {
    //  代码
    },
   removeListener: function(el, type, fn) {
    // 代码
   },
   getEvent: function(e) {
   // 代码
   }
  
   // 还可以添加其他的属性和方法
}

//使用addListener方法的写法:
MYAPP.event.addListener("yourel", "type", callback);
```

### 16. Modules

ES6 is the first time that JavaScript has built-in modules.

#### 16.1 Multiple named exports

```
//------ lib.js ------
export const sqrt = Math.sqrt;
export function square(x) {
    return x * x;
}
export function diag(x, y) {
    return sqrt(square(x) + square(y));
}

//------ main.js ------
import { square, diag } from 'lib';
console.log(square(11)); // 121
console.log(diag(4, 3)); // 5
```

#### 16.2 Single default export

```
//------ myFunc.js ------
export default function () { ··· } // no semicolon!

//------ main1.js ------
import myFunc from 'myFunc';
myFunc();
```

```
//------ MyClass.js ------
export default class { ··· } // no semicolon!

//------ main2.js ------
import MyClass from 'MyClass';
const inst = new MyClass();
```
#### 17 关于This

学习this的第一步是明白：

1. this既不指向函数自身也不指向函数的'词法作用域'，抛开以前的错误假设和理解；
2. this实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用；

