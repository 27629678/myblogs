#TableViewCell之动画设计

###一、 iOS系统自带动画

系统自带删除动画太丑，不能满足交互设计师及视觉设计师的要求，这就需要开发的同学自己想办法把需要的动画做出来


###二、动画设计方法

从Cell入手，把每个要做的动画放在Cell上。比如，删除时需要Cell滑向左边框，然后再从上堆叠下来。这个例子，可以设计如下：

- Cell的背影层不动
- ContentView做左移动画
- ContentView全部移动到边界外面后，TableView再执行Delete操作
