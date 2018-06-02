## WKWebView的优缺点

### 一、优点

1. JIT：支持（Just in Time）编译，且运行速度比`UIWebView`快3倍（官方说法）；
2. Crash Isolation：崩溃隔离，使用单独的进程进行渲染；
3. 支持IndexedDB，还未了解是什么东西；

### 二、缺点：

与`UIWebVIew`相比，缺点如下：

1. 不可以通过`URL Loading System`拦截（intercept）网络请求；
2. 没有Cookie和LocalStorage管理API；

[参考链接](http://www.iphonehacks.com/2015/01/google-chrome-ios-doesnt-use-ios-8-wkwebview-offer-improved-performance-due-to-significant-technical-limitations.html)