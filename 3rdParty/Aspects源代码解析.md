## Aspects源代码解析

### 0x00 用途

AOP，Objective-C语言下面向切面的编程的轻量级实现；

### 0x01 实现（剥洋葱）

#### 1.1 入口

```
static id aspect_add(id self, SEL selector, AspectOptions options, id block, NSError **error) {
	// 上自旋锁
	spin_lock_closure() {
		if (!can_hook()) {
			return nil;
		}
		
		hook();
	}
}

```

自旋锁闭包直接略过；

#### 1.2 是否允许Hook

常用工具函数：

- aspect_aliasForSelector(SEL selector) - 生成方法的别名，如将`test:`->`aspects_test:`;

##### 1.2.1 不允许hook的方法(黑名单)

- retain
- release
- autorelease
- forwardInvocation

暂时就这些方法不可被Hook；

##### 1.2.2 受限的dealloc方法

若被hook的方法是`dealloc`方法，插入的位置只能是`AspectPositionBefore`，其它方法没有此限制；

##### 1.2.3 是否是已经实现的方法

被hook的方法必须是类方法或者实例方法，即满足以下条件：

```
[self respondsToSelector:selector] || [self.class instancesRespondToSelector:selector]
```

##### 1.2.4 类（Class）继承层级检查

主要检查`Class`是不是一个`MetaClass`，即如下代码所示：

```
if (class_isMetaClass(object_getClass(self))) {
	// do some check work
}
```

该类型检查几乎遇不到，暂时先不分析，略过；

> **NOTE:**目前实现没有其它限制。

#### 1.3 aspect_container

一个关联（associated object）到被hook的对象上的一个属性，包含了该对象所有hook的信息，以`alias_method`为`key`，以`container_obj`为`value`，其`container`的属性及方法如下所示：

- beforeAspects
- insteadAspects
- afterAspects

\- (BOOL)removeAspect:(id)aspect;

\- (void)addAspect:(AspectIdentifier *)aspect withOptions:(AspectOptions)injectPosition;

#### 1.4 method swizzling

大体的步骤如下：

1. 创建`Cls_Aspects_`类，添加相关方法
2. 向`Cls_Aspects_`类添加被`hook_method`的方法，并将该方法的实现**IMP**指向`_objc_msgForward`
3. 向`Cls_Aspects_`类添加`aspects_hooked_method`，并将该方法的实现**IMP**指向`origin_hook_method_imp`
4. 替换`Cls_Aspects_`类的`ForwardInvocation`方法的实现，指定自定义`_ASPECTS_ARE_BEING_CALLED_()`

> **NOTE:**`_ASPECTS_ARE_BEING_CALLED_()`的IMP是比较重要的，切面的实现主要来自这里，但是也有坑，最后的结论会涉及；

#### 1.5 \_ASPECTS\_ARE\_BEING\_CALLED\_的实现

根据上面提到的`aspect_container`对象找到`before`，`instead`和`after`要执行的操作，然后在_ASPECTS_ARE_BEING_CALLED_方法内按顺序执行，就实现了切面编程；

具体的实现看源代码即可，代码思路相当清楚；

### 0x02 实现类似JSPatch的方法替换

略，具体实现与实例方法替换类似；

### 0x03 与JSPatch一起使用遇到的坑

因为两个库在实现过程中都使用相同的实现原理，即将被`hook_method`的实现指向`_objc_msgForward`，并将`ForwardInvocation`方法的实现指向自定义的实现，无论hook的顺序谁先谁后都会引发怪异的问题，如：

- 找不到对应的selector（crash）
- 方法没有被正确的替换（还好不会崩）

### 0x04 解决方案

待两个库的作者共同解决该问题；