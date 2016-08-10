## 1. 使用SQLite进行多进程共享数据


## 2. 使用NSUserDefaults进行多进程共享数据


## 3. 使用NSFileCoordinator和NSFilePresenter

It's important to be aware that NSFileCoordinator methods run synchronously, so your code will block until they complete.

```
NSFileCoordinator *fileCoordinator = [[NSFileCoordinator alloc]
    initWithFilePresenter:self];
NSError *fileCoordinatorError = nil;
__block NSArray *savedNotes = nil;

[fileCoordinator coordinateReadingItemAtURL:[self demoNoteFileURL] options:0
    error:&fileCoordinatorError byAccessor:^(NSURL *newURL) {
   
    NSData *savedData = [NSData dataWithContentsOfURL:newURL];
   
    if (savedData != nil) {
        savedNotes = [NSKeyedUnarchiver unarchiveObjectWithData:savedData];
    }
}];
```

> **NOTE:** However, you must not use file coordination APIs directly for this in iOS 8.1.x and earlier.

<!-- References: -->

[Post](http://www.atomicbird.com/blog/sharing-with-app-extensions)