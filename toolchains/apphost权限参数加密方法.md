#### 邮箱大师活动链接更新策略

设置页面内的活动页面不采用硬链接，为了便于页面内容的更新及**apphost**权限的变更决定采用定期的更新的方式，这样即不会影响已经部署的旧页面的正常体验，也不会出现页面功能的缺失。

#### apphost权限加密方式

一个普通的`URL`如下所示：

```
https://host/path?query

query = param1:value1&param2:value2&apphost:hostlevel
```

将`https://host/path`与`apphost:hostlevel`混合生得出结果为`https://host/path?apphost:hostlevel`，将该串使用私钥进行加密并做`Base64`变换作为公开`URL`参数中的值进行传输；

优点：

1. `apphostlevel`加密后产生的不是固定的值；
2. 客户端使用公钥进行验证并解出真正的`apphost`的`level`;

缺点：页面被拦截重新指向其它页面，如何保证`apphost`不被恶意访问；