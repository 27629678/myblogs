1、UITableView执行插入和删除Cell时要执行beginUpdates和endUpdates方法

```
[aTableView beginUpdates];

// do something
// insert/delete/reload mutipule rows to be animated simultaneusly

[aTableView endUpdates];
```