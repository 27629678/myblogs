## 查看上线包ipa中的Provision（配置文件）和Entitlement（授权文件）

### 目的

确认上线包中的配置是否已经正确设置

### 方法

1. 导出ipa包；
2. 解压，或者进入ipa内的文件夹内；
3. 使用`codesign`工具查看`entitlement`；

```
$ codesign -d --entitlements :- "Payload/YourApp.app"
```

4. 使用`security`工具查看`embedded.mobileprovision`;

```
$ security cms -D -i "Payload/YourApp.app/embedded.mobileprovision"
```