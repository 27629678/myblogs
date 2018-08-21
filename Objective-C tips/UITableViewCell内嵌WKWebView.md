## UITableViewCell内嵌WKWebView

### 1. 如何获取WebView内容高度

#### 1.1 KVO监听ScrollView的ContentSize

该方法可以，但是调用太频繁，不推荐使用；

#### 1.2 WebView回调

利用WebView完成内容加载的回调方法内获取`scrollview`的高度，但是获取的结果不一定是正确的结果，高度不推荐使用；

#### 1.3 使用JavaScript获取Dom的高度

通过tableView的eval脚本的能力获取执行结果的值，如下所示：

```
webView?.evaluateJavaScript("document.body.scrollHeight", completionHandler: { (value, error) in
            // handle value and error
        })
```

> NOTE:综合多方面考虑，建议使用第三种方法

### 2. 防止TableView抖动

鉴于TableView的缓存机制对于WebView的变高支持必须缓存每个Cell的高度，可以尝试不缓存效果；

### 3. Cell高度变化之后更新


在第一节中，Cell高度变化之后就可以更新高度了，下面的方法不会重新构造`cell`，但是会更新每个可见`cell`的高度，相当trick的技巧，文档没有提及；如果谁发现官方文档请联系我；

```
table.beginUpdates()
// do nothing
table.endUpdates()

```

> NOTE:以上方法还欠缺一点体验，快速滑动的时候WebView内容会变白之后再加载出内容来，若cell相当少，比如个位数可以选择缓存webview实例；