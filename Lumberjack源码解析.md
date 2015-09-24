##· Lumberjack源码解析

####1、掩码枚举类型

```
typedef NS_OPTIONS(NSUInteger, DDLogFlag) {
    DDLogFlagError      = (1 << 0), // 0...00001
    DDLogFlagWarning    = (1 << 1), // 0...00010
    DDLogFlagInfo       = (1 << 2), // 0...00100
    DDLogFlagDebug      = (1 << 3), // 0...01000
    DDLogFlagVerbose    = (1 << 4)  // 0...10000
};

typedef NS_ENUM(NSUInteger, DDLogLevel) {
    DDLogLevelOff       = 0,
    DDLogLevelError     = (DDLogFlagError),                       // 0...00001
    DDLogLevelWarning   = (DDLogLevelError   | DDLogFlagWarning), // 0...00011
    DDLogLevelInfo      = (DDLogLevelWarning | DDLogFlagInfo),    // 0...00111
    DDLogLevelDebug     = (DDLogLevelInfo    | DDLogFlagDebug),   // 0...01111
    DDLogLevelVerbose   = (DDLogLevelDebug   | DDLogFlagVerbose), // 0...11111
    DDLogLevelAll       = NSUIntegerMax                           // 1111....11111 (DDLogLevelVerbose plus any other flags)
};
```

>使用**NSUIntegerMax**代替**(-1)**，虽然结果一样，但是使用**NSUIntegerMax**更容易真实表达意思。

####2、返回给定Path的文件名

```
/**
 * The THIS_FILE macro gives you an NSString of the file name.
 * For simplicity and clarity, the file name does not include the full path or file extension.
 *
 * For example: DDLogWarn(@"%@: Unable to find thingy", THIS_FILE) -> @"MyViewController: Unable to find thingy"
 **/

NSString * DDExtractFileNameWithoutExtension(const char *filePath, BOOL copy);

#define THIS_FILE         (DDExtractFileNameWithoutExtension(__FILE__, NO))

NSString * DDExtractFileNameWithoutExtension(const char *filePath, BOOL copy) {
    if (filePath == NULL) {
        return nil;
    }

    char *lastSlash = NULL;
    char *lastDot = NULL;

    char *p = (char *)filePath;

    while (*p != '\0') {
        if (*p == '/') {
            lastSlash = p;
        } else if (*p == '.') {
            lastDot = p;
        }

        p++;
    }

    char *subStr;
    NSUInteger subLen;

    if (lastSlash) {
        if (lastDot) {
            // lastSlash -> lastDot
            subStr = lastSlash + 1;
            subLen = (NSUInteger)(lastDot - subStr);
        } else {
            // lastSlash -> endOfString
            subStr = lastSlash + 1;
            subLen = (NSUInteger)(p - subStr);
        }
    } else {
        if (lastDot) {
            // startOfString -> lastDot
            subStr = (char *)filePath;
            subLen = (NSUInteger)(lastDot - subStr);
        } else {
            // startOfString -> endOfString
            subStr = (char *)filePath;
            subLen = (NSUInteger)(p - subStr);
        }
    }

    if (copy) {
        return [[NSString alloc] initWithBytes:subStr
                                        length:subLen
                                      encoding:NSUTF8StringEncoding];
    } else {
        // We can take advantage of the fact that __FILE__ is a string literal.
        // Specifically, we don't need to waste time copying the string.
        // We can just tell NSString to point to a range within the string literal.

        return [[NSString alloc] initWithBytesNoCopy:subStr
                                              length:subLen
                                            encoding:NSUTF8StringEncoding
                                        freeWhenDone:NO];
    }
}
```

####3、指定文件非ARC警告

```
#if !__has_feature(objc_arc)
#error This file must be compiled with ARC. Use -fobjc-arc flag (or convert project to ARC).
#endif
```

####4、NSProcessInfo类

封装了CPU、内存及系统等信息的获取API

####5、stdlib.h

```
// register a function to be called on exit
int	 atexit_b(void (^)(void)) __OSX_AVAILABLE_STARTING(__MAC_10_6, __IPHONE_3_2);
```

####6、使用GCD信号量

```
// usr/include/dispatch/semaphore.h
// A counting semaphore
// create a semaphore with no less than 0 value
// Passing zero for the value is useful for when two threads need to reconcile
// the completion of a particular event. Passing a value greather than zero is
// useful for managing a finite pool of resources, where the pool size is equal
// to the value.
dispatch_semaphore_t dispatch_semaphore_create(long value);

// decrement the counting semaphore,
// if semaphore is less than 0,
// it will block the thread until time out
long dispatch_semaphore_wait(dispatch_semaphore_t dsema, dispatch_time_t timeout);

// signal a semaphore
// increment the semaphore
// If the previous value was less than zero,
// this function wakes a waiting thread before returning.
long dispatch_semaphore_signal(dispatch_semaphore_t dsema);
```

