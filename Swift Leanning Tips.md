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