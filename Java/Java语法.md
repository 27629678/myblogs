## 1、关键字

### 1.1 final

#### 1.1.1 修饰field

值（或引用的对象）不可修改；

```
// 运行时常量
final ClassA a = new ClassA();
final static ClassA a = new ClassA();

// 编译常量
public static final String CURRENT_VERSION = "1.0";
```

> NOTE:允许出现`final ClassA a;`类似的表达式，但是必须在`默认构造函数`内进行初始化，确保该`field`在使用之前已经被正确初始化，否则编译时会报错；

#### 1.1.2 修饰参数

```
public void foo(final String msg) { ... }
```

以上代码中形参`msg`在调用时，实参在方法体不可被修改；

#### 1.1.3 修饰方法

```
final public void foo() { ... }
```

以上代码禁止在子类中被**重写**；

#### 1.1.4 修饰类Class

```
final public class FOO { ... }
```

以上代码表示类FOO禁止被**继承**；