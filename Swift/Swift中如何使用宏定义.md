## 一、为Swift在工程文件中添加「宏定义」

在XCode 8.0之前，可以在工程文件的`Build Settings`选项卡内，搜索`Preprocessor Macros`设置项内，一般可以看到`Debug`和`Release`两个可设置项，用于Objective-C语言可使用的**宏定义**，用于条件编译等等；

在XCode 8.0及以后，新增加`Active Compilation Conditions`设置项，与`Preprocessor Macros`类似，前者用于Swift语言内可使用的**宏定义**；

另外一个为Swift添加**宏定义**的位置是`Other Swift Flags`；

## 二、Swift如何使用Objective-C语言的宏定义

解决文案参考：[From Objective-C to Swift：Use Complex #define Macros in Swift](https://medium.com/@YogevSitton/from-objective-c-to-swift-use-complex-define-macros-in-swift-28cdff464fc7)

大体实现就是在宏定义地方，再为Swift定义一个类方法，具体如下所示：

```
// Constants.h

#define SCREEN_WIDTH [UIScreen mainScreen].bounds.size.width
+ (CGFloat) ScreenWidth;

// Constants.m
+ (CGFloat) ScreenWidth { return SCREEN_WIDTH; }

// *.swift
self.tableFrame.size.width = Constants.ScreenWidth();
```