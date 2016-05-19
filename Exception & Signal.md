## 未捕获的异常与信号处理

### Objective-C异常捕获

使用`NSSetUncaughtExceptionHandler`方法注册一个异常处理函数，用于处理未捕获的异常；

```
- (void)safeRegisterUncaughtExceptionHandler
{
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        __ne__register_uncaught_signal_handler();
        self.originExceptionHandler = NSGetUncaughtExceptionHandler();
        NSSetUncaughtExceptionHandler(&__ne__uncaught_exception_handler);
    });
}
```

> NOTE：使用`NSGetUncaughtExceptionHandler`方法获取`Origin`异常处理函数，在新注册的函数中调用`Origin`用于防止先前注册过的函数失效；

### Kernel异常捕获

使用`void (*signal(int sig_num, void(*handler)(int)))(int)`方法捕获异常`信号`

```
static sig_t origin_uncaught_signal_handler = NULL;

void __ne__register_uncaught_signal_handler()
{
    origin_uncaught_signal_handler = signal(SIGSEGV, __ne__uncaught_signal_handler);
}

void __ne__uncaught_signal_handler(int signal_num)
{
    NSString* reason = [NSString stringWithFormat:@"%ld", (long)signal_num];
    NSException* exception = [NSException exceptionWithName:@"signal"
                                                     reason:reason
                                                   userInfo:@{}];
    if ([[NEExceptionHandler defaultHandler] enabled]) {
        [[NEExceptionHandler defaultHandler] fireWithException:exception];
    }
    
    signal(signal_num, origin_uncaught_signal_handler);
}
```