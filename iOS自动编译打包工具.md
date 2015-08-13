#ios自动编译打包工具

###iOS 自动打包教程

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



[Source Code @github](https://github.com/lexrus/ios-makefile)


[1]:https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/xcodebuild.1.html


引用的文章：

- http://www.cnblogs.com/brycezhang/p/4097487.html

- http://www.2cto.com/kf/201312/262436.html
- http://www.shangxueba.com/jingyan/1841475.html

- http://my.oschina.net/LangZiAiFer/blog/299374?p=1