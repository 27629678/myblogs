## UIWindow中遇到的坑

### 1. keyWindow的默认行为

引发的问题：

inputView文法在becomeFirstResponder之后没有被调用，UIResponder的shouldReloadInputViews文法返回false，无法加载显示inputView；

问题定位：

alert，actionsheet或者keyboard等出现后，keyWindow更换为当前的window，消失之后再恢复为原应用的window。

默认行为：

若原window已经隐藏或者不存在，则选取当前应用中最项层的window作为keywindow。
