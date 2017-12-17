## Mastering CMake

### 二、Hello World for CMake

#### 2.3 Basic CMake Usage and Syntax

The CMake language is expressed as a series of commands. Each command is evaluated in the order that it appears in the `CMakeLists.txt` file.

```
cmmand(args ...)
```

- **command**: the name of the command, case insensitives after version 2.2;
- **args**: is a **white-space separated** list of arguments;

```
// args list
// Foo = [a, b, c] in common programming language
set (Foo a b c)	

// using args list, syntax below
add_executable(demo ${Foo})	// demo is the executable target
```

环境变量读取方法：

```
// kind of linux/unix
$ENV{VAR}

// windows
[HKEY_CURRENT_USER\\Software\\path1\\path2;key]
```