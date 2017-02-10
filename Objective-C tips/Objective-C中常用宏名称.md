## Objective-C中常用宏名称

### 1. NS\_REQUIRES\_NIL\_TERMINATION

实例：

```
- (void)appendToMyTail:(NSString *)values, ...NS_REQUIRES_NIL_TERMINATION {
    if (nil != values) {
        self = [self stringByAppendingString:values];
        NSString *tempStr;
        va_list varList;
        va_start(varList, values);
        while (tempStr = va_arg(varList, NSString *)) { //以NSString内存大小为偏移量取下一个值。
            self = [self stringByAppendingString:tempStr];
        }
        va_end(varList);
    } 
}

//invoke
NSString *str = @"";
[str appendToMyTail:@"my", @"name", @"is", @"Chiling", nil];
//str  "my name is Chiling"
```