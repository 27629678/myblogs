## 你不知道的宏定义

### 1. 双#号的功能

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
