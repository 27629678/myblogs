## APNs注意事项

### 一、Notifications

`iOS_8.0`及以后的版本加入了自定义的推送通知数据扩展，如下所示`Payload`Json:

```
{
	aps: {
		content-avaliable:1,
	},
	action-id:1,	// customized content
}
```

#### 1.1、UILocalNotification

- `iOS`或者`OSX`系统最多可以调度64个本地通知，按`时间`保留最先被`fire`的通知；
- `重复通知`（Recurring notifications，如每天一次的循环通知）算一条通知计算；

> Each app on a device is limited to 64 scheduled local notifications. The system discards scheduled notifications in excess of this limit, keeping only the 64 notifications that will fire the soonest. Recurring notifications are treated as a single notification.

#### 1.2、Remote Notifications

##### 1.2.1、Device Token

###### DeviceToken何时变更

1. 针对同一台设备，`DeviceToken`是系统级别的，不同的`APP`获得的Token是相同的；
2. 安装新的`APP`或者更新`APP`不会引发`DeviceToken`的更新；
3. 从`备份`的数据恢复到设备上也不引发`DeviceToken`的更新；
4. `恢复出厂设置`会导致`DeviceToken`的更新；

待补充

### 二、Multi-Tasking

`iOS_7.0`系统的升级引入了该特性，大大提升了后台代码的执行能力；

> **NOTE：**该特性要求`APP`存在于`多任务列表`中，即双击`HOME`键所见到的应用列表，**用户手动清理掉**的应用程序无法享用该特性。

#### 2.1、Backgroud Fetch

- 执行时机：由系统在**合适**的时机执行
- 执行时间：每次最长30秒

该后台任务的执行与其它`APP`共用链接，又由于执行时间短，不适合CPU密集或者长时间运行的任务，所以该特性适合做**小**的网络获取等任务；

系统会跟踪用户的使用习惯，如每晚22：00打开`微博APP`，`BackgroundFetch`会在22：00点之前提前之行，以便新的内容提前下载供用户阅读，**该功能未验证其存在的真实性**；

#### 2.2、Silent Push Notification

静默推送与普通的远程推送不同，该通知不会弹出提醒Banner或者Alert，消息中心也看不到该通知，更没有声音提示；若`APP`存在于多任务列表中，接收到该类型的通知后，系统会唤醒应用（即看不到应用的UI，UI可以做离屏渲染）；开启方式：在`Info.plist`中的`UIBackgroundModes`中添加`remote_notification`，也可以在工程配置中的`Capability`选项卡中开启，并设置`Payload`的内容如下所示：

```
{
	aps: {
		content-avaliable:1,
	}
}
```

> **NOTE：**推送频率受限，`APNs`会阻塞过多的推送（每小时个位数的推送不会受限，但是并没有得到验证）

#### 2.3、Background Transfer

该特性使用`NSURLSession`进行网络数据传输，**仅**在`Wi-Fi`网络环境下运行；使得应用即使不在前台（用户手动杀掉进程除外）运行，大的下载/上传任务也可以继续运行；

这里要注意一下`NSSessionTask`，分为三种：

1. `DataTask`
2. `DownloadTask`
3. `UploadTask`

> **NOTE：**后台传输不支行`DataTask`类型

除此之外，`NSURLSession`要使用特殊的配置，即`[NSURLSessionConfigration backgroundSessionConfigration]`；

其它待补充……

### 三、同步时机

#### 3.1、未读数同步

- `applicationDidEnterBackground:`，若没有成功，重新同步账号和未读数

#### 3.2、DeviceToken的订阅

- 添加账号
- 标记账号删除
- `IMAP`账号更改服务器配置
- `BackgroundFetch`中调用`manualCheckPushTriger:`方法

#### 3.3、通知提醒项设置同步

- 设置页中提醒配置变更时同步



