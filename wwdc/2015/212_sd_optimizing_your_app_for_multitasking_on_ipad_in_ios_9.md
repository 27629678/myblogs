#高级适配内存

- NSPurgeableData

在iOS的内存中，数据分为三种类型：

- InUse/Dirty
- Purgeable
- Clean


- Absolutely Essential
- Expensive to Generate
- Can be pre-generated
- static once generated


###- Mapped Data

- Memory Mapped Data

API:
```
typedef NS_OPTIONS(NSUInteger, NSDataReadingOptiongs) {
  NSDataReadingMappedIfSafe,
  NSDataReadingMappedAlways,
}

@interface NSData (NSDataCreation)

- (nullable instancetype)initWithContentOfFile:(NSString *)path
                                       options:(NSDataReadingOptiongs)readOptionsMask
                                         error:(NSError **)errorPtr;

@end
```

> NOTE: Don't block main-thread in multiple-tasking environment
