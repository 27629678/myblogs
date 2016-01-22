#URL Loading System

##·About the URL Loading System

该教程将描述使用标准协议实现使用URL与服务器交互，`URL Loading System`聚集在一起形成标准库（Foundation Framwork）。

`URL Loading System`由一系列类和协议组成，该系统最核心的类是`NSURL`，App通过操作NSURL对象可以访问特定的资源（Resource)。

为了支持`NSURL`，标准库提供了丰富的资源用来实现加载URL指定的资源，上传数据到服务器，管理Cookie，控制缓存及处理认证等。除此之外，还可以自定义扩展协议。

`URL Loading System`支持以下协议：

- 文件传输协议(ftp://)
- 超文本传输协议(http://)
- 带加密的超文本传输协议(https://)
- 本地文件URL(file://)
- Data URL(data://)

one more thing, 它也支持代理服务器和Socks网关。


> **Important:** 除此之外, OS X and iOS提供了API接口供其它应用程序打开URL，比如Safari，这些API接口在此不再作详细介绍。

- For more information about Launch Services in OS X, read Launch Services Programming Guide.
- For more information about the `openURL:` method in the NSWorkSpace class in OS X, read NSWorkspace Class Reference.
- For more information about the `openURL:` method in the UIApplication class in iOS, read [UIApplication Class Reference][1].

###·At a Glance

`URL Loading System`如下图所示：

<!--此处发表时要取消注释, 图片链接服务器在国外-->
<!--![url_loading_system][2]-->

####-URL Loading

该类簇是`URL Loading System`最常使用的类簇，用于App索引URL指定的资源。在OS X或者iOS系统中，App可以通过多种方式实现检索数据，这主要依靠App的需求，比如希望检索数据文件、检索内存数据块等：

- iOS7或者OS X 10.9之后，推荐使用`NSURLSession`来实现网络请求
- 使用`NSURLDownload`下载文件到磁盘上
- 使用`NSURLConnection`先将文件下载到内存中，再写到磁盘上

> **Note:**具体选择哪个实现方法根据是否选择下载文件到磁盘还是到内存中。

#####-Fetching Content as Data (In Memory)

使用高级API接口，有两种方法实现检索数据：

- 对于简单的请求，使用`NSURLConnection`接口获取NSURL指定的数据以Data形式存入内存或者以文件的形式存入磁盘
- 对于复杂的请求（如上传文件等），可以自定义`NSURLRequest(NSMutableURLRequest)`通过`NSURLSession`或者`NSURLConnection`直接下载

忽略具体请求的实现方法，可以通过以下两种方式获取网络响应数据：

- 完成回调(block)，当完成网络请求时调用该回调方法
- 代理回调(delegate method)，调用具体的需要调用具体的代理方法

#####-Downloading Content as a File
使用高级API接口，有两种方法实现下载URL指定的文件：

- 对于简单的请求，使用`URLSession`接口以**NSData**或文件的形式直接下载URL指定的资源
- 对于复杂的请求，可以自定义`NSURLRequest`通过`NSURLSession`或`NSURLDownload`下载URL指定的资源

与`NSURLDownload`相比，`NSURLSession`具有如下两个优点：

- iOS系统下有效
- 当App挂起、结束运行或者崩溃后，可以继续在后台下载（比较逆天）

> **Note:**由`NSURLDownload`和`NSURLSession`发起的下载实例并不缓存，若要缓存结果，应用程序必须使用`NSURLConnection`或`NSURLSession`将下载结果写入磁盘。

####-Helper Classes

`URL Loading System`提供了两个帮助类：

- `NSURLRequest`
- `NSURLResponse`

####-Redirection and Other Request Changes

如Http协议等，`URL Loading System`为服务器提供了一下方式用于告知App某资源已经变更为另外一个URL，App可以按需自定义实现该协议方法。

####-Authentication and Credentials

有些服务器对资源的访问是有限制的，为访问服务器资源App需要提供一些身份用于认证——证书、用户名及密码等等。以Web Server为例，需要一套认证凭证才可以访问限制内容，同时证书也用于App验证是否信任该服务器。

`NSURLCredential`类封装了一个证书，该证书包含身份验证信息(例如用户名和密码)和持久性的行为。`NSURLProtectionSpace`类代表一个区域, 需要特定的证书。

`NSURLAuthenticationChallenge`类封装了一个`NSURLProtocol`实现所需的信息进行身份验证请求: 约定的凭据，保护空间，错误或响应等，这些是协议认证所必须的。一个`NSURLAuthenticationChallenge`实例还指定了身份验证的对象，也被称为发送者，该对象必须实现`NSURLAuthenticationChallengeSender`协议。

####-Cache Management

`URL Loading System`提供了基于App为单位的硬盘和内存缓存用于减少App对网络连接的依赖和快速处理先前缓存的响应。`NSURLRequest`对象初始化时指定了`NSURLConnection`查询缓存的策略。

`NSURLCache`类提供了配置缓存大小和存储位置的方法，也提供了管理`NSCachedURLResponse`对象的方法。

`NSCachedURLResponse`类封装了`NSURLResponse`类和URL指定的资源，也提供了**User Info Dictionary**用于自定义处理服务器响应数据。

`NSURLConnection`类可用于控制响应是否被缓存，并且可以通过实现`connection:willCacheResponse:`协议方法用决定是否在内存中缓存响应。

> **NOTE:**不是所有协议都支持缓存机制，目前仅支持Http和Https协议。

####-Cookie Storage

归于Http协议的无状态的特性，App经常使用Cookie将`NSURL`请求的数据持久化存储，`URL Loading System`提供了接口用于创建和管理Cookie，发送Cookie作为Request的一部分，接收Cookie当解析服务器响应时。（这句话翻译地好别扭）

在OS X和iOS系统下，`NSHTTPCookieStorage`类提供了管理Cookie的接口。不同之处有两点：

- 在OS X下，Cookie是跨应用共享的
- 在iOS下，Cookie是基于应用独立使用的

####-Protocol Support

`URL Loading System`本身提供了对`http`、`https`、`file`、`ftp`和`data`协议的支持。然后`URL Loading System`也提供了App注册自定义继承自`NSURLProtocol`的类用于支持特定的协议。

-----------------------------

##·Using NSURLSession

`NSURLSession`类及相关的类提供了通过**HTTP协议**下载数据内容的API接口，为了支持认证及后台下载（例如App没有运行，或者在iOS系统下App被挂起）还提供了丰富的**Delegate Methods(代理方法)**。

要使用`NSURLSession`，App要创建一系列`NSURLSession`对象用于协调每一组数据传输任务。例如创建一个浏览器，要为每一个Tab面或者窗口（Window）创建一个`NSURLSession`对象。在每一个`NSURLSession`对象中，App可以添加一系列任务，每个任务都代表一个指定URL的请求（Request）。

与大多数的网络接口一样，`NSURLSession`提供的接口是高度异步的。如果使用系统默认的Delegate方法，App需要提供一个Finish Handler用于返回数据传输完成后的数据或者Error。相反，如果App提供了自定义的Delegate方法，任务对象会在从服务器接收到数据后调用这些自定义Delegate方法。

`NSURLSession`还提供了**状态**和**进度**两个属性供App进行查询，另外还提供了支持取消、重启和暂停任务的接口。

###-Understanding URL Session Concepts

####-Types of Sessions

- Default sessions
- Ephemeral sessions
- Background sessions

####-Types of Tasks

- Data tasks
- Downloads tasks
- Upload tasks

####-Background Transfer Considerations

####-Life Cycle and Delegate Interaction

####-NSCopying Behavior

###-Sample Delegate Class Interface

code snippet:

```
#import <Foundation/Foundation.h>
 
 
typedef void (^CompletionHandlerType)();
 
@interface MySessionDelegate : NSObject <NSURLSessionDelegate, NSURLSessionTaskDelegate, NSURLSessionDataDelegate, NSURLSessionDownloadDelegate>
 
@property NSURLSession *backgroundSession;
@property NSURLSession *defaultSession;
@property NSURLSession *ephemeralSession;
 
#if TARGET_OS_IPHONE
@property NSMutableDictionary *completionHandlerDictionary;
#endif
 
- (void) addCompletionHandler: (CompletionHandlerType) handler forSession: (NSString *)identifier;
- (void) callCompletionHandlerForSession: (NSString *)identifier;
 
 
@end

```

###-Creating and Configuring a Session

code snippet:Creating and configuring sessions

```
#if TARGET_OS_IPHONE
    self.completionHandlerDictionary = [NSMutableDictionary dictionaryWithCapacity:0];
#endif
 
    /* Create some configuration objects. */
 
    NSURLSessionConfiguration *backgroundConfigObject = [NSURLSessionConfiguration backgroundSessionConfiguration: @"myBackgroundSessionIdentifier"];
    NSURLSessionConfiguration *defaultConfigObject = [NSURLSessionConfiguration defaultSessionConfiguration];
    NSURLSessionConfiguration *ephemeralConfigObject = [NSURLSessionConfiguration ephemeralSessionConfiguration];
 
 
    /* Configure caching behavior for the default session.
       Note that iOS requires the cache path to be a path relative
       to the ~/Library/Caches directory, but OS X expects an
       absolute path.
     */
#if TARGET_OS_IPHONE
    NSString *cachePath = @"/MyCacheDirectory";
 
    NSArray *myPathList = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES);
    NSString *myPath    = [myPathList  objectAtIndex:0];
 
    NSString *bundleIdentifier = [[NSBundle mainBundle] bundleIdentifier];
 
    NSString *fullCachePath = [[myPath stringByAppendingPathComponent:bundleIdentifier] stringByAppendingPathComponent:cachePath];
    NSLog(@"Cache path: %@\n", fullCachePath);
#else
    NSString *cachePath = [NSTemporaryDirectory() stringByAppendingPathComponent:@"/nsurlsessiondemo.cache"];
 
    NSLog(@"Cache path: %@\n", cachePath);
#endif
 
 
 
 
 
    NSURLCache *myCache = [[NSURLCache alloc] initWithMemoryCapacity: 16384 diskCapacity: 268435456 diskPath: cachePath];
    defaultConfigObject.URLCache = myCache;
    defaultConfigObject.requestCachePolicy = NSURLRequestUseProtocolCachePolicy;
 
    /* Create a session for each configurations. */
    self.defaultSession = [NSURLSession sessionWithConfiguration: defaultConfigObject delegate: self delegateQueue: [NSOperationQueue mainQueue]];
    self.backgroundSession = [NSURLSession sessionWithConfiguration: backgroundConfigObject delegate: self delegateQueue: [NSOperationQueue mainQueue]];
    self.ephemeralSession = [NSURLSession sessionWithConfiguration: ephemeralConfigObject delegate: self delegateQueue: [NSOperationQueue mainQueue]];
    
```

code snippet:Creating a second session with the same configuration object

```
    ephemeralConfigObject.allowsCellularAccess = YES;
 
    // ...
 
    NSURLSession *ephemeralSessionWiFiOnly = [NSURLSession sessionWithConfiguration: ephemeralConfigObject delegate: self delegateQueue: [NSOperationQueue mainQueue]];

```

####-Fetching Resources Using System-Provided Delegates

code snippet:Requesting a resource using system-provided delegates

```
     NSURLSession *delegateFreeSession = [NSURLSession sessionWithConfiguration: defaultConfigObject delegate: nil delegateQueue: [NSOperationQueue mainQueue]];
 
    [[delegateFreeSession dataTaskWithURL: [NSURL URLWithString: @"http://www.example.com/"]
                       completionHandler:^(NSData *data, NSURLResponse *response,
                                           NSError *error) {
                           NSLog(@"Got response %@ with error %@.\n", response, error);
                           NSLog(@"DATA:\n%@\nEND DATA\n",
                                 [[NSString alloc] initWithData: data
                                         encoding: NSUTF8StringEncoding]);
                       }] resume];

```

####-Fetching Data Using a Custom Delegate

code snippet:Data task example

```
    NSURL *url = [NSURL URLWithString: @"http://www.example.com/"];
 
    NSURLSessionDataTask *dataTask = [self.defaultSession dataTaskWithURL: url];
    [dataTask resume];
```

####-Downloading Files

code snippet:Download task example

```
    NSURL *url = [NSURL URLWithString: @"https://developer.apple.com/library/ios/documentation/Cocoa/Reference/"
                  "Foundation/ObjC_classic/FoundationObjC.pdf"];
 
    NSURLSessionDownloadTask *downloadTask = [self.backgroundSession downloadTaskWithURL: url];
    [downloadTask resume];
```

code snippet:Delegate methods for download tasks

```
-(void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didFinishDownloadingToURL:(NSURL *)location
{
    NSLog(@"Session %@ download task %@ finished downloading to URL %@\n",
        session, downloadTask, location);
 
#if 0
    /* Workaround */
    [self callCompletionHandlerForSession:session.configuration.identifier];
#endif
 
#define READ_THE_FILE 0
#if READ_THE_FILE
    /* Open the newly downloaded file for reading. */
    NSError *err = nil;
    NSFileHandle *fh = [NSFileHandle fileHandleForReadingFromURL:location
        error: &err];
 
    /* Store this file handle somewhere, and read data from it. */
    // ...
 
#else
    NSError *err = nil;
    NSFileManager *fileManager = [NSFileManager defaultManager];
    NSString *cacheDir = [[NSHomeDirectory()
        stringByAppendingPathComponent:@"Library"]
        stringByAppendingPathComponent:@"Caches"];
    NSURL *cacheDirURL = [NSURL fileURLWithPath:cacheDir];
    if ([fileManager moveItemAtURL:location
        toURL:cacheDirURL
        error: &err]) {
 
        /* Store some reference to the new URL */
    } else {
        /* Handle the error. */
    }
#endif
 
}
 
-(void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didWriteData:(int64_t)bytesWritten totalBytesWritten:(int64_t)totalBytesWritten totalBytesExpectedToWrite:(int64_t)totalBytesExpectedToWrite
{
    NSLog(@"Session %@ download task %@ wrote an additional %lld bytes (total %lld bytes) out of an expected %lld bytes.\n",
        session, downloadTask, bytesWritten, totalBytesWritten, totalBytesExpectedToWrite);
}
 
-(void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didResumeAtOffset:(int64_t)fileOffset expectedTotalBytes:(int64_t)expectedTotalBytes
{
    NSLog(@"Session %@ download task %@ resumed at offset %lld bytes out of an expected %lld bytes.\n",
        session, downloadTask, fileOffset, expectedTotalBytes);
}
```

####-Uploading Body Content

#####-Uploading Body Content Using an NSData Object

#####-Uploading Body Content Using a File

#####-Uploading Body Content Using a Stream

#####-Uploading a File Using a Download Task

####-Handling Authentication and Custom TLS Chain Validation

####-Handling iOS Background Activity

code snippet:Session delegate methods for iOS background downloads

```
#if TARGET_OS_IPHONE
-(void)URLSessionDidFinishEventsForBackgroundURLSession:(NSURLSession *)session
{
    NSLog(@"Background URL session %@ finished events.\n", session);
 
    if (session.configuration.identifier)
        [self callCompletionHandlerForSession: session.configuration.identifier];
}
 
- (void) addCompletionHandler: (CompletionHandlerType) handler forSession: (NSString *)identifier
{
    if ([ self.completionHandlerDictionary objectForKey: identifier]) {
        NSLog(@"Error: Got multiple handlers for a single session identifier.  This should not happen.\n");
    }
 
    [ self.completionHandlerDictionary setObject:handler forKey: identifier];
}
 
- (void) callCompletionHandlerForSession: (NSString *)identifier
{
    CompletionHandlerType handler = [self.completionHandlerDictionary objectForKey: identifier];
 
    if (handler) {
        [self.completionHandlerDictionary removeObjectForKey: identifier];
        NSLog(@"Calling completion handler.\n");
 
        handler();
    }
}
#endif
```

code snippet:App delegate methods for iOS background downloads

```
- (void)application:(UIApplication *)application handleEventsForBackgroundURLSession:(NSString *)identifier completionHandler:(void (^)())completionHandler
{
    NSURLSessionConfiguration *backgroundConfigObject = [NSURLSessionConfiguration backgroundSessionConfiguration: identifier];
 
    NSURLSession *backgroundSession = [NSURLSession sessionWithConfiguration: backgroundConfigObject delegate: self.mySessionDelegate delegateQueue: [NSOperationQueue mainQueue]];
 
    NSLog(@"Rejoining session %@\n", identifier);
 
    [ self.mySessionDelegate addCompletionHandler: completionHandler forSession: identifier];
}
```

-------------------------

##·Using NSURLConnection

（持续翻译中）

[原文链接][n]

<!--链接变量:短链接-->
[1]:http://t.cn/RLXd4wM
[2]:http://t.cn/RLXdRhx
[n]:http://t.cn/RLapoO7