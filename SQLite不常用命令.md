## SQLite不常用命令

### 0x01、Pragma

**PRAGMA**语句是SQLite的扩展功能，用于查询和编辑SQLite内部数据（非数据表），功能上与**SELECT**和**INSERT**的功能相似；

#### 1. database_list

查询**Attach**到当前连接的数据库名称和文件路径；

```
- (NSDictionary *)dbname2path
{
    NSString* sql = @"pragma database_list";
    FMResultSet* rs = [self.db executeQuery:sql];
    NSMutableDictionary* attachedDB = [NSMutableDictionary dictionary];
    while (rs.next) {
        NSString* dbName = [rs objectForColumnIndex:1];
        NSString* dbPath = [rs objectForColumnIndex:2];
        
        if (dbName.length * dbPath.length == 0) {
            continue;
        }
        
        [attachedDB setObject:dbPath forKey:dbName];
    }
    
    return attachedDB;
}
```

### 0x02 预写日志（Write-Ahead Log，简称WAL）

此日志模式用于解决多线程（或多Connection）环境下，并发读写提升throughput（吞吐量）的；

但是该模式存在性能的问题，比如设置预写日志PageSize的大小（默认是1000），要根据应用程序的读写频率自定义该pagesize的大小，否则可能比非WAL模式下的性能更差；

```
// 显示日志模式
PRAGMA journal_mode;

// 设置日志模式
PRAGMA journal_mode=WAL;
```

> NOTE：设置为WAL后，数据库重新打开后日志模式不会被重置，是永久设置的；

### 0x03 线程模型

- Single Thread，非常线程安全的，不可多线程并发读写操作；
- Multi-Thread，线程安全的，可并发读写，写是的排它的操作；
- Serialized，线程安全的，按顺序执行读写操作；

可以在编译阶段和数据库打开阶段设置，也可以在运行时修改；

> NOTE：若编译阶段设置为Single-Thread模式，则在打开和运行时不可以升级为多线程和串行执行模式；