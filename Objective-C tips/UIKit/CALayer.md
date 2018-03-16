## CALayer Programming Guide

### 一、基本内容

`CALayer`是一个`bitmap`的数据模型，不直接参与视图在屏幕上的渲染，做到了很好的解耦（MacOS X的NSView默认没有Layer对象，iOS默认基于CALayer实现的）；主要包含以下两部分数据：

- 位图数据
- 状态信息

以上数据由GPU等图形硬件资源进行处理，这也是为什么比`UIView`的`drawRect:`方法性能高的原因；因为，后者使用CPU资源在主线程绘制图形内容转化为位图数据再交给GPU等硬件资源处理；

Layer对象主要分以下三类：

- Model Layer Tree：保存最终状态信息
- Presentation Layer Tree：当前屏幕上显示的状态信息
- Render Layer Tree：私有树，供渲染使用

### 二、设置

设置视图使用专用Layer方式：

```
// 重写UIView的以下方法设置专用layerClass
+ (Class)layerClass
{
		return [_ASDisplayLayer class];
}
```

以下主要讲一下自定义绘制内容的方式：

#### 2.1 直接设置位图

```
		myLayer.contents = some_bitmap_contents; // @CGImageRef
```

#### 2.2 设置CALayerDelegate

##### 2.2.1 实现displayLayer:协议方法

方法原型如下所示，函数体内通过设置`layer`实例的`contents`属性即可完成；

```
- (void)displayLayer:(CALayer *)theLayer {
    // Check the value of some state property
    if (self.displayYesImage) {
        // Display the Yes image
        theLayer.contents = [someHelperObject loadStateYesImage];
    }
    else {
        // Display the No image
        theLayer.contents = [someHelperObject loadStateNoImage];
    }
}
```

##### 2.2.2 实现drawLayer: inContext:协议方法

在函数体内实现在`CGContextRef`上下文进行图形绘制并提交即可完成；

```
- (void)drawLayer:(CALayer *)theLayer inContext:(CGContextRef)theContext {
    CGMutablePathRef thePath = CGPathCreateMutable();
 
    CGPathMoveToPoint(thePath,NULL,15.0f,15.f);
    CGPathAddCurveToPoint(thePath,
                          NULL,
                          15.f,250.0f,
                          295.0f,250.0f,
                          295.0f,15.0f);
 
    CGContextBeginPath(theContext);
    CGContextAddPath(theContext, thePath);
 
    CGContextSetLineWidth(theContext, 5);
    CGContextStrokePath(theContext);
 
    // Release the path
    CFRelease(thePath);
}
```

> NOTE：若Delegate的以上两个方法都实现，仅调用`displayLayer:`方法；

##### 2.2.3 派生CALayer的子类

重写以下方法：

- display方法：使用该方法直接设置`contents`属性；
- drawInContext:方法：在给定的`GraphicContext`上绘制对应的图形；

### 三、动画

主要支持以下两种动画：

1. 基于属性的动画
2. 关键帧动画

### 四、其它

其它详细内容[点击链接](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40004514-CH1-SW1)。