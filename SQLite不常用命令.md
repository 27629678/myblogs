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