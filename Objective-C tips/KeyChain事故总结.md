## KeyChain事故总结

从iOS 1.0版本到现在，`DeviceUniqueID`的获取经历了N个历史时代，从最初的`[UIDevice uniqueDeviceID]`发展到`Mac Address`，再发展到`APNs Push Token`，后来推送Token也随时会变化，最终我们把`DeviceUniqueID`存储到`KeyChain`中，经历几年的使用，一直“相安无事”

> NOTE：因为一直没有关注过生产环境中`DeviceUniqueID`稳定情况，所以一直“相安无事”；

直到有一天，新的需求下来了，`**服务登录凭证`内保存`DeviceUniqueID`用于校验该设备的请求是否有效，灰度上线后，发生了惨不忍睹的事情，`DeviceUniqueID`会发生跳变，即A->B(或其它），用户重新启动后再变回A；

经历了两个版本的修复文案：

1. 从备份中恢复；
2. 添加KeyChain统计数据；

最终的结果都开始怀疑程序员的人生，萌发了想去开发`Android`想法；

从统计数据上看，我们做了如下怀疑：

1. 系统不稳定，请观看电影《绝对安全的系统》；
2. 文件系统抖动，应用启动各个阶段没有读写沙箱外文件的权限；
3. KeyChainItemWrapper因年久失修存在Bug；
4. 面对未知技术的恐惧，无知地空想（经历N手的代码）；

但是，冷静下来理智的思考一下，如此大的量出现KeyChain读写问题决非系统的问题，而是代码的Bug或者使用姿势不对；决定把当年WWDC的视频和相关文档再翻一遍；果不其然，KeyChain读写是有限制的，kSecItemAttrAccessible如下：

- kSecAttrAccessibleWhenUnlocked：设备解锁成功后可读写，可备份，**默认值**；
- kSecAttrAccessibleAfterFirstUnlock：系统启动第一次解锁成功后可读写，可备份；
- kSecAttrAccessibleAlways：随时可以读写，可备份；
- kSecAttrAccessibleWhenPasscodeSetThisDeviceOnly：系统设置解锁密码，仅该设备有效且不可备份；
- kSecAttrAccessibleWhenUnlockedThisDeviceOnly：设备解锁成功后可读写，仅该设备有效且不可备份；
- kSecAttrAccessibleAfterFirstUnlockThisDeviceOnly：系统启动第一次解锁成功后可读写，仅该设备有效且不可备份；
- kSecAttrAccessibleAlwaysThisDeviceOnly：随时可以读写，仅该设备有效且不可备份；

当看到以上内容时，大脑中突然想到了`后台刷新`可导致生产环境中的用户应用程序在设备锁定的状态下后台启动运行，把`KeyChainItemWrapper`的源代码翻了几遍，确实没有设置这个属性；

经过一翻实验，想哭的心都有了（终于找到原因了）；

> NOTE：上面的属性不可设置查询的接口内，否则永远在KeyChain存储中读取不到数据，返回errSecItemNotFound；