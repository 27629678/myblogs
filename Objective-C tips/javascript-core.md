## JavascriptCore基本使用

### 0x01 JSContext

**JSContext**提供了一个`Javascript`的执行环境，与DOM对象中的Window一样；

### 0x02 JSValue

提供了`Javascript`与`Obj-C/Swift`数据类型的转换，并绑定到**JSContext**对象上；

- \- (BOOL)toBool;
- \- (double)toDouble;
- \- (int32_t)toInt32;
- \- (uint32_t)toUInt32;
- \- (NSNumber \*)toNumber;
- \- (NSString \*)toString;
- \- (NSDate \*)toDate;
- \- (NSArray \*)toArray;
- \- (NSDictionary \*)toDictionary;
- \- (id)toObject;
- \- (id)toObjectOfClass:(Class)expectedClass;

```
NSString* factorialScript = loadFactorialScript();
[_context evaluateScript: factorialScript];
JSValue *function = _context[@"factorial"];
JSValue *result = [function callWithArguments:@[@5]];
NSLog(@"factorial(5) = %d", [result toInt32]);
```
