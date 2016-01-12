###XLForm

####1、Rows

- input rows:提供了文本输入功能，并配置了不同的键盘类型、自动修正及自动大小写等
- selector rows:提供了push/action sheet/alert view/picker view等视图
- date & time rows:提供了设置日期和时间的Cell，并支持inlined style
- boolean rows:提供了两种类型Bool控制器:switch and check
- other rows:
	- stepper
	- slider
	- info
	- button

####2、Multivalued Sections(insert, delete, reorder rows)

####3、Form Values

invoking `- (NSDictionary *)formValues`, the dictionary key is the value of `XLFormRowDescriptor`'s `tag` property.

####4、Create a Custom Row

can create a `UITableViewCell` extending from `XLFormBaseCell`, which conforms to `XLFormDescriptorCell` protocol.

#####4.1、Custom Selector

create Selector row with a custom selector view controller.