## 1、__attribute__((weak))的作用

```
// power.h

// weak declaration
int power2(int x) __attribute__((weak));

// strong declaration
int power3(int x);

```

该属性用于标识在链接过程中采用weak的方式进行链接（默认为strong，即在目标文件中找到对应的「符号」则进行下一个符号的链接），若后续目标文件中有weak（或strong）符号，则覆盖之前的符号，直到搜索结束；

> NOTE: 不是所有的编译器都支持该属性，以上描述只是为快速理解weak的作用，真实地链接过程并不是如上所述进行；

[参考链接Weak Symbol](https://en.wikipedia.org/wiki/Weak_symbol)
