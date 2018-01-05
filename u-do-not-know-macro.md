## 你不知道的宏定义

### 1. 双#号的功能-Token粘贴操作符

可能大家对双#号的了解是合并前后内容，例如：

```
#define merge(a, b) a##b

int main() {
  printf("%d\n", merge(1, 2));
  printf("%s\n", merge("ab", "cd"));

  return 0;
}

# 12
# "abcd"
```

其实，双#号还有一个比较罕见，但很重要的功能，就是在多个参数或者可变参数的宏函数定义中，当可变参数为空时，吃掉前面的**逗号**，例如：

```
#define NSLog(format, ...) \
do {  \
  printf(format, ##__VA_ARGS__);  \
}while(0) \

// 若可变参数的个数为空时
NSLog("hello, world.");

// gcc -E main, 若在可变参数个数为空时，双#号没有吃掉前面逗号的功能宏展开后
do {
  printf("hello, world.",);
}while(0);

// 以上代码会编译失败，因为第一个参数后面的逗号引起编译错误
// 所以，正确的展开如下所示：
do {
  printf("hello, world.");
}while(0);

```
### 2. Objective-C中的单#号

```
/*!
 * @define XCTAssertTrue(expression, ...)
 * Generates a failure when ((\a expression) == false).
 * @param expression An expression of boolean type.
 * @param ... An optional supplementary description of the failure. A literal NSString, optionally with string format specifiers. This parameter can be completely omitted.
*/
#define XCTAssertTrue(expression, ...) \
    _XCTPrimitiveAssertTrue(self, expression, @#expression, __VA_ARGS__)
```

在上面的代码中，单#号的作用是将expression字符化，即#expression -> "expression", C字符串，再在前面加一个@（objc的编译符号）即@"expression"转化为NSString类型；

注意事项：

- \#操作符并不是简单的增加双引号，它会自动对特殊符号进行转义
- \#操作符只能对func-like的参数使用
- 由于参数会转化为 token 列表，所以前后的空白符都会被忽略，中间的空白符会被压缩为一个，注释会被忽略并变成一个空白符。