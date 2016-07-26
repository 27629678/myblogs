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

- public: 当前模块，或者其它模块都可以访问，通常用于Framework；
- private: 只访问当前源文件内的内容，若当前源文件有多个类，可以访问；
- internal: default access level，当前模块的其它源文件都可以访问；
- @testable: unit test

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