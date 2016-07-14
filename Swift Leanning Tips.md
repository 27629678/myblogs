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

> **NOTE:**If an implicitly unwrapped optional is nil and you try to access its wrapped value, you’ll trigger a runtime error. The result is exactly the same as if you place an exclamation mark after a normal optional that does not contain a value.

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
### x Error Handling

available after Swift 2.0