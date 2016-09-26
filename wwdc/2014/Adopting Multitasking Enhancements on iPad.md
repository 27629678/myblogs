#Adopting Multitasking Enhancements on iPad


###- 摘要

为了适配App支持分屏模式，首先要在**Info.plist**文件中添加**UIRequiresFullScreen**并设置其值为**YES**。

开启画中画做法如下：

- For the AVPlayerViewController class, set the allowsPictureInPicturePlayback property value to NO.
- For the AVPlayerLayer class, do not instantiate an AVPictureInPictureController with your player layer object.
- For the WKWebView class, set its allowsPictureInPictureMediaPlayback property to NO. (This property is available starting in a future beta version of iOS 9.)

###- 升级工程文件

从旧版本工程升级到iOS的SDK，若要支持分屏功能需要做如下配置：

- 设置SDK为最新的版本
- 提供一个**LaunchScreen.storyboard**文件，不是iOS7或者更早版本中的**PNG图片**文件，[配置方法][1]|[创建方法][4]
- 在**Info.plist**文件中设置**Supported interface orientations**支持所有朝向
- 在**Info.plist**文件中添加**UIRequiresFullScreen**并设置其值为**YES**
- 实现[UITraitEnvironment][5]和[UIContentContainer][6]协议

实例代码下载地址：[Sample Code Project][2]

###- Something you do not know

在分屏模式下，Primary和Secondary应用程序都运行在前台且对于系统来讲大多数情况下拥有相同的地位，Primary App拥有以下特权：

- 拥有状态栏
- 有资格显示在副屏上
- 可以参与画中画自动调用
- 在横屏模式下可以占用2/3的屏幕

> **NOTE:**所有屏幕尺寸的变化通知都发送给各个App的**Root View Controller**的**Size Class**去处理。

###- 分屏模式下的屏幕尺寸

R&C如图所示：

![屏幕尺寸][3]








[1]:https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW4

[2]:https://developer.apple.com/library/prerelease/ios/samplecode/Lister/Introduction/Intro.html#//apple_ref/doc/uid/TP40014701

[3]:https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/Art/multitasking-size-classes_2x.png

[4]:https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW4

[5]:https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitEnvironment_Ref/index.html#//apple_ref/occ/intf/UITraitEnvironment

[6]:https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIContentContainer_Ref/index.html#//apple_ref/occ/intf/UIContentContainer