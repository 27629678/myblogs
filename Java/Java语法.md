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

### 1.2 Exception

`Java`语言内分为以下两种类型的异常：

1. **Checked**：只能在`try-catch`代码块内调用可能抛出**checked**异常的方法
2. **Unchecked**：不一定需要`try-catch`代码块内；

> NOTE：[Here's the bottom line guideline: If a client can reasonably be expected to recover from an exception, make it a checked exception. If a client cannot do anything to recover from the exception, make it an unchecked exception.](https://docs.oracle.com/javase/tutorial/essential/exceptions/runtime.html)，官方文档最底部。

## 2. 注解（Anatations）

JDK 1.5引入注解概念；

### 2.1 应用于Java代码的注解

1. **@Override**：编译阶段，编译器检查该方法是否重写了父类（或接口）中的方法；
2. **@Deprecated**：标记该方法被淘汰了，若代码中继续使用，编译器在编译阶段给出警告；
3. **@SuppressWarnings**：告诉编译器对该注解修饰的参数不提示任何警告；其中，错误除外；

### 2.2 无注解（MetaAnatations）

1. **@Retention**：保留位置，枚举值为`Class`（默认），`Runtime`和`Source`，详见`RetentionPolicy`定义；
2. **@Documented**：是否出现在文档中；
3. **@Target**：作用目标，枚举值为`TYPE`，`FIELD`，`METHOD`，`PARAMETER`，`CONSTRUCTOR`，`LOCAL_VARIABLE`，`ANNOTATION_TYPE`和`PACKAGE `，详见`ElementType`定义；
4. **@Inherited**：说明子类是否可继承父类的注解；

### 2.3 新注解

1. **@SafeVarargs**：方法参数和泛型构造函数禁用警告，Java7引入；
2. **@Repeatable**：指明注解在同一个位置可被应用多次，Java8引入；
3. **@FunctionalInterface**：指明类型定义为`函数式接口`专用，Java8引入；