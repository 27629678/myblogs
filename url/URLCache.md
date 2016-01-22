###NSUrlCache

####用途

减少服务器请求次数，提升离线或者低速网络中的使用体验，iOS\_2\_0以后引入。

####如何使用

`application:didFinishLaunchingWithOptions:` for iOS

`applicationDidFinishLaunching:` for OS X

在以上方法内添加以下代码段：

启用缓存：

```
// 等价于[NSUrlCache sharedCache]
// 4MB内存, 20MB硬盘, Path:~/Library/Caches/(bundle_id)/
// 若要自定义缓存大小使用下面的方法即可
NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                     diskCapacity:20 * 1024 * 1024
                                                         diskPath:nil];
[NSURLCache setSharedURLCache:URLCache];
```

自定义缓存Resp对象：

```
- (NSCachedURLResponse *)connection:(NSURLConnection *)connection
                  willCacheResponse:(NSCachedURLResponse *)cachedResponse
{
	// 返回自定义的Resp
	return [[NSCachedURLResponse alloc] initWithResponse:[cachedResponse response]
                                                   data:mutableData
                                               userInfo:mutableUserInfo
                                          storagePolicy:storagePolicy];
   // 不缓存Resp
   	return nil;
}
```

> NOTE:如果不实现此方法，NSURLConnection 就简单地使用本来要传入 -connection:willCacheResponse: 的那个缓存对象，所以除非你需要改变一些值或者阻止缓存，否则这个代理方法不必实现。