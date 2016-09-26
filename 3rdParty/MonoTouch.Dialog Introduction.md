###MonoTouch.Dialog

####Introduction

MonoTouch.Dialog, 以下简称MT.D，C#封装库，提供了声明式语法创建iOS应用UI的简洁方法，由以下几个UI组件组成：

- DialogViewController
- RootElements
- Section
- Element

可以嵌套使用，并提供了JSon及URL加载UI界面的方法。

嵌套方式如下：

```
RootElements:
	Section:
		element or new RootElements
	... ...
	
	// 每一个新的RootElements都可以创建一个新DVC
```

两套API供使用，如下所示：

- Low-Level Elements API：基于创建UI元素层级树的方式来呈现
- High-Level Reflection API：基于反射（或绑定）的方式创建UI元素来呈现

####Built-In Elements

- Bool:Switch
- Float:Slider
- String:左边显示标题，右边展示`string`的值
- Styled String:可以自定义如下属性：
	- `Font`
	- `Text Color`
	- `Background Cell Color`
	- `Line Breaking Mode`
	- `Number of Lines to Display`
	- `Accessory Indicator`
- Entry Element:
	- KeyBoardType:Numberic,Phone,Email,Url
	- PlaceHolder
	- Password
- Multiline Element:显示一个标题+多行文本
- CheckBox Element:勾选框
- Radio Element:单选框
- Badge Element:图片+标题
- Activity Element:一个简单的显示`Activity Indicator`的Cell
- Date Element:标题+日期 -> Date Picker
- Time Element:标题+时间 -> Time Picker
- DateTime Element:标题+日期时间 -> Date & Time Picker
- HTML Element:标题，点击后进入WebView加载HTML内容
- Message Element:如短信息或邮件列表上的Cell一致
- Load More Element:加载更多Cell，提供加载更多触发事件和加载完成回调
- UIViewElement:自定义UIView实例