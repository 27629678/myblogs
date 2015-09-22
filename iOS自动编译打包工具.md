#ios自动编译打包工具

###一、XCode配置方法

####1、xcconfig文件

目标：解耦工程配置文件

#####- 关于xcconfig 文件

`xcconfig`文件是一个用来保存`build setting`键值对的纯文本文件, 这些键值对会覆盖`build setting`中的值，所以当你在`xcconfig`文件中配置了的选项在`build setting` 中保持默认值就好了（因为是什么都不会起作用了）。

#####- 如何使用

点击工程文件，切到`info Tab`,定位到`Configurations`选项（实际上这个 tab 下的选项并不多，一眼就瞄到了），点开`Configurations`前面的小箭头，看到了分为`Debug`和`Release`，分别点击`Debug`与`Release`选择对应的 xcconfig 文件（Debug 对应 Debug.xcconfig，Release 对应 Release.xcconfig）。

####2、给类起个别名

**@compatibility_alias**

This is a feature of the Objective-C compiler rather than of the runtime, anyway since it is documented nowhere and its existence was forgotten, we are documenting it here.

The keyword @compatibility_alias allows you to define a class name as equivalent to another class name. For example:

```
@compatibility_alias WOApplication GSWApplication;
```

tells the compiler that each time it encounters WOApplication as a class name, it should replace it with GSWApplication (that is, WOApplication is just an alias for GSWApplication).

There are some constraints on how this can be used--

- WOApplication (the alias) must not be an existing class;
- GSWApplication (the real class) must be an existing class.

>NOTE:`@compatibility_alis`是用于给一个类设置一个别名,这样就不用重构以前的类文件就可以用新的名字来替代原有名字。

####3、@import的使用

与#import的区别：

- 编译时自动导入系统的`Framework`
- 比`#import`更加高效
- 可以导入部分模块，如`@import Foundation.NSString`

>NOTE:@import主要使用在写framework时使用，平时写应用程序建议不要使用

####4、添加警告

1、普通警告

```
#warning TODO
```

2、接口弃用警告

```
- (void)addTapAction:(SEL)tapAction target:(id)target NS_DEPRECATED_IOS(2_0, 4_0);
```

3、接口附带警告信息

```
- (void)addTapAction:(SEL)tapAction target:(id)target __attribute((deprecated("这个接口会爆内存 不建议使用")));
```

###二、iOS 自动打包教程

打包常用命令行工具（如何下载请自行脑补）：xcodebuild和xcrun。

用到的关键命令

- 编译workspace

	`xcodebuild -workspace workspacename -scheme schemename -configuration [-configuration configurationname] clean build SYMROOT=(SYMROOT)`

- 编译project

	`xcodebuild -target targetname -configuration [-configuration configurationname] clean build SYMROOT=(SYMROOT)`

- 查看配置信息

	`xcodebuild -list`

- xcrun打包ipa

	`xcrun -sdk iphoneos PackageApplication -v projectName.app -o ipaName.ipa`

- xcodebuild：负责编译，[官方介绍][1]
- xcrun：负责将app打包成ipa

命令常用格式如下：

```
xcodebuild -project aPreject -scheme aScheme -destination generic/platform=iOS archive -archivePath aPath
xcrun -sdk iphoneos PackageApplication -v appOriginDirectory -o ipTargetDirectory -s aCodeSign
```

脚本编写思路：

- build ipa
- itms-service protocol
- upload ipa(ftp/sftp)

ipa-build:  编译xcode工程并生成ipa文件
ipa-publish: 生成符合itms-services协议的文件，并发布到服务器。
sendEmail:  stmp发送email的脚本。（别人写的）
sftpDownloadFile: 通过sftp协议下载文件
sftpUploadFile: 通过sftp协议上传文件
updateLocalIndexHtml:   对索引文件进行处理(二进制文件，非shell脚本)
uploadItemsServicesFiles:   将itms-services协议文件上传到服务器

[Source Code @github](https://github.com/lexrus/ios-makefile)

>NOTE:如果您的应用构建需要使用scheme，请确保使用移动服务平台前，如下配置您的目标scheme。
>1 Xcode进入product-> scheme -> manage schemes
>2 将您的目标scheme 后面的 shared 勾选框勾中
>3 提交以上修改到代码库中

[stackoverflow](http://stackoverflow.com/questions/14368938/xcodebuild-says-does-not-contain-scheme)

引用的文章：

- http://www.cnblogs.com/brycezhang/p/4097487.html

- http://www.2cto.com/kf/201312/262436.html
- http://www.shangxueba.com/jingyan/1841475.html

- http://my.oschina.net/LangZiAiFer/blog/299374?p=1


<!--引用链接-->
[1]:https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/xcodebuild.1.html
