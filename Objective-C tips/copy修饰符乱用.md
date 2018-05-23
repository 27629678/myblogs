## Copy修饰符乱用

下面的代码大家应该很熟悉：

```
@property (nonatomic, copy) NSString *name;
```

会生成下面等价的`setter`方法：

```
- (void)setName:(NSString *)name
{
  // 忽略ARC代码
  _name = [name copy];
}
```

> 看到这里，感觉没有问题，但是如果类型为`NSMutable*****`的话，结果就是属性的值会变成一个`Frozen`对象，而最终在运行时不可被编辑；
