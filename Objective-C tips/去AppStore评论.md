## 去AppStore评论

一般，我们知道如何根据`appid`跳转到商店进行展示，如下所示：

```
NSURL *url = [NSURL urlWithString:@"https://itunes.apple.com/cn/app/id1234?mt=8"];
[UIApplication sharedApplication] openURL:url];

# 即可完成跳转到商店内展示id为1234的应用程序
```

接下来介绍一下如何跳转到**应用程序评论页面**的方法，如下所示：

```
NSURL *url = [NSURL urlWithString:@"https://itunes.apple.com/cn/app/id1234?mt=8&action=write-review"];
[UIApplication sharedApplication] openURL:url];

# 即可完成跳转到商店内展示id为1234的应用程序的评论页面，无需复杂的操作就可以直接进入评论页面
```