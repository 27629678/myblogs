```
+ (NSString *)hardwardInfo
{
    NSProcessInfo* process = [NSProcessInfo processInfo];
    NSString* os_version = process.operatingSystemVersionString;
    unsigned long long memory_length = process.physicalMemory / (1000 * 1000);
    NSInteger processor_count = process.processorCount;
    NSString* doc = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).lastObject;
    NSDictionary* attributes = [[NSFileManager defaultManager] attributesOfFileSystemForPath:doc error:NULL];
    float total_size = [attributes[NSFileSystemSize] floatValue];
    float free_size = [attributes[NSFileSystemFreeSize] floatValue]/(1000 * 1000);
    NSString* free_size_string = nil;
    if (free_size < 1000) {
        free_size_string = [NSString stringWithFormat:@"%.2fMB", free_size];
    }
    else {
        free_size_string = [NSString stringWithFormat:@"%.2fGB", free_size/1000];
    }
    
    return [NSString stringWithFormat:@"processor count:%llu"
            "\nmemory:%ldMB"
            "\ndisk space:%.2fGB"
            "\ndisk free space:%@"
            "\nos version:%@",
            (unsigned long long)processor_count,
            (long)memory_length,
            total_size/(1000*1000*1000),
            free_size_string,
            os_version];
}
```