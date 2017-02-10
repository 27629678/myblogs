## Swift Leanning Tips

### 0x01 Basics

#### 1.1 Hello, world!

```
print("hello, world!")

print(variable)

print("hello, \(variable)")
```

#### 1.2 Constants & Variables

```
let x = 0

var y = x

x = 1	// error, can not assign a new value to constant

y  = 1 // ok

let x = y = z = 0	// ?

var a = b = c = 0	// ?

```

#### 1.2 Type Annotations

```

// Swift's basic types inclue: Int, Double, Bool and String.

var msg: String = "hello"

var red, green, blue: Double

let pi = 3.1415926	// Double type by default

```

#### 1.3 Basic Operator

```

// Range Operators

// closed range operator(a...b)

(1...3) // 1, 2, 3

// half-open range operator(a..<b)
(1..<3)	// 1, 2

```

> **NOTE:**注意**点**的个数

#### 1.4 Optionals

```
var statusCode: Int? = 404	// statusCode is a integer value or nil, can not be other types of value

statusCode = nil	// ?

// the potential value for statusCode is nil or Int Type value

var x: Int

print(x)	// error, can not use uninitilized variable

// the potential value for x is Int Type value, MUST NOT assign nil to x

foo(statusCode)	// compiling error, value of not unwrapped;
// did you mean to use "!" or "?" ?

foo(statusCode!) 	// Implicitly Unwrapped Optionals

// unitary ! operator only apply on unitary ? operator decorated variable, force unwrap the variable as mentioned before

/// A type that can represent either a `Wrapped` value or `nil`, the absence
/// of a value.
public enum Optional<Wrapped> : _Reflectable, NilLiteralConvertible {
    case None
    case Some(Wrapped)
    /// Construct a `nil` instance.
    public init()
    /// Construct a non-`nil` instance that stores `some`.
    public init(_ some: Wrapped)
    /// If `self == nil`, returns `nil`.  Otherwise, returns `f(self!)`.
    @warn_unused_result
    public func map<U>(@noescape f: (Wrapped) throws -> U) rethrows -> U?
    /// Returns `nil` if `self` is `nil`, `f(self!)` otherwise.
    @warn_unused_result
    public func flatMap<U>(@noescape f: (Wrapped) throws -> U?) rethrows -> U?
    /// Create an instance initialized with `nil`.
    public init(nilLiteral: ())
}

```

#### 1.5 Optional Binding

You use optional binding to find out whether an optional contains a value, and if so, to make that value available as a temporary constant or variable. Optional binding can be used with if and while statements to check for a value inside an optional, and to extract that value into a constant or variable, as part of a single action.

Write an optional binding for an if statement as follows:

```
if let constantName = someOptional {
    statements
}
else {
	// do something if optional value is nil
}

```

#### 1.6 Implicitly Unwrapped Optionals

```
var obj: MyClass!
```

> **NOTE：**隐式解包的Optional和普通的Optional变量没有任何区别，只是在访问变量时编译器会自动在变量后插入「!」符号进行解包；但是，若obj为nil时，访问会导致crash；

You can think of an implicitly unwrapped optional as giving permission for the optional to be unwrapped automatically whenever it is used. Rather than placing an exclamation mark after the optional’s name each time you use it, you place an exclamation mark after the optional’s type when you declare it.

```
let possibleString: String? = "An optional string."
let forcedString: String = possibleString! // requires an exclamation mark

let assumedString: String! = "An implicitly unwrapped optional string."
let implicitString: String = assumedString // no need for an exclamation mark
```

#### 1.7 Selectors

```
let mySelector = #selector(MyViewController.tappedButton)
```

> **NOTE:**If an implicitly unwrapped optional is nil and you try to access its wrapped value, you’ll trigger a runtime error. The result is exactly the same as if you place an exclamation mark after a normal optional that does not contain a value.

#### 1.8 AnyObject

```
var possibleDate: AnyObject = someDate()!
let date = possibleDate as? NSDate	// DownCasting AnyObject, date may be a nil value if possibleDate is not NSDate Type
let date1 = possibleDate as! NSDate	// Force DownCasting AnyObject, may trigger runtime error when possibleDate is not NSDate Type
```

#### 1.9 DoubleQuestionMark

```
var c = a ?? b	// c = a != nil ? a! : b
```

#### 1.10 Optional Chaining

```
var str: String?
str = "Hello, playground"
print(str?.characters.count)

// Optional(17)

// using optinal binding
if let count = str?.characters.count {
    print(count)
}

// 17
```

> **NOTE:** The value, is optional, which is returned from optional chaining expression

#### 1.11 ?? operator and @autoclosure

```
var str: String?
let str1 = str ?? "Hello, playground"
let str2 = str != nil ? str : "Hello, playground"
```

implementations

```
public func ??<T>(optional: T?, defaultValue: @autoclosure () throws -> T) rethrows -> T
public func ??<T>(optional: T?, defaultValue: @autoclosure () throws -> T?) rethrows -> T?
```

#### 1.12 @escaping and @nonescaping

If a closure is passed as an argument to a function and it is invoked after the function returns, the closure is escaping.

#### 1.12.1 Benefits

There are several benefits to make closures non-escaping by default. The most obvious benefits are performance and the ability for the compiler to optimize your code. If the compiler knows that a closure is non-escaping, it can take care of a many of the nitty-gritty details of memory management.

This also means that you can use the self keyword without issues in non-escaping closures because the closure is invoked before the function returns. There is no need to use a weak reference to self in the closure. This is a nice benefit you get for free.

#### 1.13 as as! as?的区别

##### 1.13.1 upcasting

```
class Animal {}
class Cat: Animal {}
let cat = Cat()
let animal = cat as Animal
```

##### 1.13.2 downcasting

```
class Animal {}
class Cat: Animal {}
let animal :Animal  = Cat()
let cat = animal as! Cat
```

区别就是使用as(?|!)时，由于是向下转型有可能会失败，使用as！时会引发运行时错误，使用as？时会赋值为nil；

[AppleDoc文档](https://developer.apple.com/swift/blog/?id=23)

#### 1.14 static | class | final

```
open class MyClass: NSObject {

	// cannot override in subclass
	static func staticMethod() {
		// do some thing
	}

	// can override in subclass
	class func classMethod() {
		// do some thing
	}

	// cannot override in subclass
	final [class] func finalMethod() {
		// do some thing
	}
}

```

> **NOTE:**从功能上讲，static与final类似，都不能在子类中重写；值得注意的是，若通过JSPatch进行热更新时，static和final修饰的方法不可被覆盖，其中final是因为不可与dynamic同时修饰同一个方法；而static类似C语言的静态方法，无法通过OC的运行时进行替换；

#### 1.15 指针传递（inout）

```
func foo(_ error: NSErrorPointer)
{
    let myError: NSError? = NSError(domain: "playground", code: 400, userInfo: nil)
    error?.pointee = myError
}

func foo1 (_ error: UnsafeMutablePointer<NSError?>?)
{
    let myError: NSError? = NSError(domain: "playground", code: 400, userInfo: nil)

    error?.pointee = myError
}

// foo2(&<#T##error: NSError?##NSError?#>), code completion carry **&** symbol automatically
func foo2(_ error: inout NSError?)
{
    let myError: NSError? = NSError(domain: "playground", code: 400, userInfo: nil)

    error = myError
}
```

#### 1.16 Any 与 AnyObject

##### 1.16.1 Any

`Swift3.0`引入的概念，用于代表**struct**，**class**及**func**等的实例；

##### 1.16.2 AnyObject

在`Swift3.0`之后，仅用于代表`class`的实例；在此之前，与`Any`一样，但是对**struct**和**func**做了隐式的桥接转换为Objective-C的对象，但是其它平台，如Linux，没有Objectvie-C的运行时库，所以，在`Swift3.0`之后又引入了`Any`用于澄清概念；

#### 1.17 @discardableResult修饰符

用于修饰`func`方法，表示该方法的返回值可以不使用，便于减少不必要的警告；

对于没有使用该修饰符的方法，可以使用如下方法消除警告：

```
_ = foo()
```

#### 1.18 变长参数

在Swift中使用变长参数，只需要在参数类型后面添加「...」即可实现，这点比Objective-C的实现更方便优雅，无需调用C方法；

```
var str = "Hello, playground"

func send(_ items: [Any]...) {
    print(items)
}

send([str])

// output
[["Hello, playground"]]

```

### 2 Control Flow & Collections

#### 2.1 Where

```
let point = (1, -1)

switch point {
	case let(x, y) where x == y:
		print("(\(x),\(y)) is on the line x == y")
	case let(x, y) where x == -y:
		print("(\(x),\(y)) is on the line x == -y")
	case let(x, y):
		print("(\(x),\(y)) is just some arbitrary point")
}
```

> **NOTE:**Swift的Switch语句不需要**break**，自动跳出；

#### 2.2 Repeat-While Loop

```
var x = 10

repeat {
	x --
} while x > 0

```

> **NOTE:**Swift's `Repeat-While` loop is simmilar to a `do-while` loop in other languages.

#### 2.3 For-in Loop

```
for idx in 1...5 {
	print(idx)
}

// 1
// 2
// 3
// 4
// 5

for idx in 1..<5 {
	print(idx)
}

// ?

for idx in 1...10 where idx > 5 {
	print(idx)
}

// ?

```

> **NOTE:**Use the `for-in` loop to iterate over a sequence, such as ranges of numbers, item in an array, or characters in a string.

#### 2.4 Control Transfer

- continue
- break
- fallthrough

```
let idx = 7

switch idx {
	case 1, 3, 5, 7:
		print("\(idx) is a prime number")
		fallthrough
	case 2, 4, 6, 8:
		print("\(idx) is an even number, ")
		if idx > 4 {
			print("\(idx) is greater than 4")
			break
		}
		else {
			print("\(idx) is less than, or equal to 4")
		}

		fallthrough
	default:
		print(", also is an integer number")
}
```

#### 2.5 map|filter|reduce

```
var array = ["a", "b", "c"]
let list = array.map { (value) -> String in
    value + value
}
let str1 = array.map { $0 + $0 }
    .filter({ (value) -> Bool in
        return value == "aa"
    })
    .reduce("") { $0 + $1 }
print(str1)

```

#### 2.6 map|flatmap

```
// collection
var array: [String] = ["1", "2", "three", "4", "//5//"]

let list0: [Int?] = array.map { (item) -> Int? in
    return Int(item)
}
print(list0)

print(array.map { Int($0) })

print(array.flatMap { Int($0) })

print(array.flatMap ({ (item) -> Int? in
    return Int(item)
}))

let list1: [Int?] = array.flatMap { Int($0) }
print(list1)

let list2: [Int?] = array.flatMap({ (item) -> Int? in
    return Int(item)
})
print(list2)

// optional

func myPrint(_ item: String)
{
    print("T:\(type(of:item)), V:\(item)")
}

let a: String? = nil

a.map(myPrint)
a.flatMap(myPrint)
```

### 3 Closures

#### 3.1 objc -> swift

```
// Objective-C block
void (^completionBlock)(NSData *, NSError *) = ^(NSData *data, NSError *error) {
   // ...
}

// Swift Closure
let completeBlock: (NSData, NSError)->void = { data, error in
	// ...
}
```

#### 3.2 Resolving Strong Reference Cycles for Closures

```
// Objective-C block
__weak typeof(self) weakSelf = self;
self.block = ^{
   __strong typeof(self) strongSelf = weakSelf;
   [strongSelf doSomething];
};

// Swift closure
self.closure = { [unowned self] in	// define a Capture List [unowned self, weak delegate = self.delegate!]
    self.doSomething()
}
```

[more details](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/AutomaticReferenceCounting.html#//apple_ref/doc/uid/TP40014097-CH20-ID57)

> **NOTE:** `__block` is default behavior for variables in Swift

### 4 Configuring Swift Interfaces in Objective-C

#### 4.1 @objc()

```
@objc(Color)
enum Цвет: Int {
    @objc(Red)
    case Красный

    @objc(Black)
    case Черный
}

@objc(Squirrel)
class Белка: NSObject {
    @objc(color)
    var цвет: Цвет = .Красный

    @objc(initWithName:)
    init (имя: String) {
        // ...
    }
    @objc(hideNuts:inTree:)
    func прячьОрехи(количество: Int, вДереве дерево: Дерево) {
        // ...
    }
}
```

> **NOTE:**Conversely, Swift also provides the @nonobjc attribute, which makes a Swift declaration unavailable in Objective-C. You can use it to resolve circularity for bridging methods and to allow overloading of methods for classes imported by Objective-C. If an Objective-C method is overridden by a Swift method that cannot be represented in Objective-C, such as by specifying a parameter to be a variable, that method must be marked @nonobjc.


#### 4.2 Access Control

`Swift`的访问控制是基于模块（module，或target）和源文件（*.swift)的，而不是基于类型和命名空间的。

- open: 比**public**级别更高, 被**open**修饰的类，可以在类定义的module之外进行子类化（即生成该类的子类）；被**open**修饰的方法，可以在方法定义的module之外进行重写（override）；「Xcode 8 beta6 引入」
- public: 当前模块，或者其它模块都可以访问，通常用于Framework；
- private: 作用域为一个{}内；「Xcode 8 beta6 更新」
- fileprivate: 只访问当前源文件内的内容，若当前源文件有多个类，可以访问；「Xcode 8 beta6 引入」
- internal: default access level，当前模块的其它源文件都可以访问；
- @testable: unit test, 可以子类化**public**修饰的类，可以重写**public**修饰的方法；

如果一个类（Class）的访问级别为`private`，则类的所有成员及方法都是private，此时成员无法修改访问级别；若一个类的访问级别为`internal`或者`public`，那么它的所有成员默认都是`internal`，此时可以单独修改成员的访问级别为`public`，即类成员的访问级别不可高于类的访问级别，子类的访问级别不高于父类的访问级别，但是在遵循三种访问级别作用范围的前提下子类可以将父类访问级别的成员重写成更高的访问级别；

> **NOTE：**在Swift里，一个`module（or Target）`就是一个命名空间，与`C#「namespace」`、`Java「package」`不同，不需要显示的指定命名空间。这样，在同一个模块内，不需要显示的导入头文件就可以互相访问，大大简化了Swift编程。

#### 4.3 Singleton Interface

```
class Singleton {
    static let sharedInstance = Singleton()
}

class Singleton {
    static let sharedInstance: Singleton = {
        let instance = Singleton()
        // setup code
        return instance
    }()
}
```

#### 4.4 Automatic Reference Counting

##### 4.4.1 weak and unowned keyword

以上两个`weak`和`unowned`关键字都可以用来解决`Strong Reference Circle`，即`循环引用`；

```
class Person {
	var name: String
	var age: Int
	weak var company: Company
}

class Company {
	var name: String
	var address: String
	unowned var boss: Person
}
```

##### 4.4.2 capture list in closure

```
var my_closure = { [weak self, unowned delegate = self.delegate!] \(name: String, age: Int) in
	// do something
}
```

[ARC Swift 文档](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AutomaticReferenceCounting.html)

### 5 Error Handling

@available(Swift 2.0, *)

#### 5.1 Representation

Any type that declares conformance to `ErrorProtocol` can be used to represent an error in Swift's **Error Handling System**

Using `enumerations` as Errors

```
enum IntParsingError: ErrorProtocol {
	case overflow
	case invalidInput(String)
}
```

#### 5.2 More Data in Errors

```
struc XMLParsingError: ErrorProtocol {
	enum ErrorKind {
		case invalidCharacter
		case mismatchedTag
		case internalError
	}

	let line: Int
	let column: Int
	let kind: ErrorKind
}
```

#### 5.3 Handling Errors

Using `do-catch` statement in the `Throwing Function`s

```
// throwing function
extension Int {

	init(validting input: String) throws {
		// ...
		if !_isValid(s) {
			throw IntParsingError.invalidInput(s)
		}
		// ...
	}

}

// calling the fucntion using 'do' statement

do {
	let price = try Int(validating:"$100")
}
catch IntParsingError.invalidInput(let invalid) {
	print("Invalid character:'\(invalid)'")
}
catch IntParsingError.overflow {
	print("Overflow error")
}
catch {
	print("Other error")
}

// Prints "Invalid character: '$'"
```

Using pattern matching to conditionally catch errors.

```
func parse(_ source: String) throws -> XMLDoc {
    // ...
    throw XMLParsingError(line: 19, column: 5, kind: .mismatchedTag)
    // ...
}

do {
    let xmlDoc = try parse(myXMLData)
}
// catch any `XMLParsingError` errors thrown by the `parse(_:)` function
catch let e as XMLParsingError {
    print("Parsing error: \(e.kind) [\(e.line):\(e.column)]")
}
catch {
    print("Other error: \(error)")
}

// Prints "Parsing error: mismatchedTag [19:5]"
```

#### 5.4 Converting Errors to Optional Values「try?」

In the following code x and y have the same value and behavior:

```
func someThrowingFunction() throws -> Int {
    // ...
}

let x = try? someThrowingFunction()

let y: Int?
do {
    y = try someThrowingFunction()
} catch {
    y = nil
}
```

#### 5.5 Disabling Error Propagation「try!」

```
// if loadImage() function return error will raise runtime error
let photo = try! loadImage("./Resources/John Appleseed.jpg")
```

#### 5.6 Specifying Cleanup Actions「defer」

You use a `defer` statement to execute a set of statements just before code execution leaves the current block of code, whether it leaves because an error was thrown or because of a statement such as return or break.

```
func processFile(filename: String) throws {
    if exists(filename) {
        let file = open(filename)
        defer {
            close(file)
        }
        while let line = try file.readline() {
            // Work with the file.
        }
        // close(file) is called here, at the end of the scope.
    }
}
```

> **NOTE:**注意与`deinit`的语义不同

### 6 Generics


### 7 Cocoa

#### 7.1 @UIApplicationMain修饰符(标签)

Main函数的入口，编译时根据模板生成main.swift文件

```
import UIKit

@UIApplicationMain
// no this modifier will raise compiling error:
// implicit entry/start for main executable
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?


    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        // Override point for customization after application launch.
        return true
    }

}
```

删除修饰符后可手动创建main.swfit文件即可正常编译通过，代码如下所示：

```
UIApplicationMain(Process.argc, Process.unsafeArgv, nil, NSStringFromClass(AppDelegate))
```

> **NOTE：**注意与Python等语言的**装饰器**功能区分开，在Swift中，仅作为编译时使用的标签，用于替换模板中的内容生成main.swift文件，并不能增强已有某方法的功能；

### 8 Type Properties

Aka `class` properties in Objective-C or other compiling languages;

```
class MyType {
	static let sharedInstance = MyType()
	static var urserInfo = "hello, world."
}
```

> **NOTE:**Unlike stored instance properties, you must always give stored type properties a default value. This is because the type itself does not have an initializer that can assign a value to a stored type property at initialization time.

> Stored type properties are lazily initialized on their first access. They are guaranteed to be initialized only once(线程安全的，所以可以用来生成单例的实例), even when accessed by multiple threads simultaneously, and they do not need to be marked with the lazy modifier.


### 9 Objective-C and Swift in same project

基本操作就不讲了，这里讲一个比较Trik的事情，「Module-Swift.h」引入时引发的编译不确定性因素。

比如，存在「ModuleA-Swift.h」和「ModuleB-Swift.h」，若一个Objective-C的源文件被两个Module访问，但是又存在编译顺序的问题，就会出现编译失败的可能。
