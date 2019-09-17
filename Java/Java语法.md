## 0、代码规范

`帕金森琐碎定律`：一个组织中的成员往往会把过多的精力花费在一些琐碎的争论上。程序员天生需要团队协作，而协作的正能量要放在问题的有效沟通上。个性化应尽量表现在系统架构和算法效率的提升上，而不是在合作规范上进行纠缠不休的讨论、争论，最后没有结论。————阿里巴巴Java手册

### 1.1 编程规约

#### 1.1.1 POJO类中布尔类型变量都不要is前缀，否则部分框架解析会引起序列化错误。

> 反例:定义为基本数据类型Boolean isDeleted;的属性，它的方法也是isDeleted，RPC框架在反向解析的时候，”误以为“对应的属性名称是deleted，导致属性获取不到，进而拋出异常。

#### 1.1.2 接口和实现类的命名有两套规则

##### 1. 对于Service和DAO类，基于SOA的理念，暴露出来的服务一定是接口，内部的实现类用Impl后缀与接口区别；

```
正例：CacheServiceImpl实现 CacheService接口;
```

##### 2. 如果是形容能力的接口名称，取对应的形容词为接口名（通常是-able的形式）

```
正例：AbstractTranslator实现Translatable接口
```

#### 1.1.3 构造方法里禁止加入任凭业务逻辑

如果有初始化逻辑，请放在init方法中；

#### 1.1.4 在循环体内，字符串的连接方式使用StringBuilder的append方法进行扩展；

> 说明：反编译出的字节码文件显示每次循环都会new出一个StringBuilder对象，然后进行append操作，最后通过toString方法返回String对象，造成内存资源浪费。

#### 1.1.5 ArrayList的subList结果不可强制转换为ArrayList，否则会抛出ClassCastException异常，如下所示：

```
java.util.RandomAccessSubList cannot be cast to java.util.ArrayList.
```

> 说明：`subList`返回的是`ArrayList`的内部类`SubList`，并不是`ArrayList`，而是`ArrayList`的一个视图，对于`subList`子列表的所有操作最终会反映到原列表上。

#### 1.1.6 在使用工具类Arrays.asList()把数组转换成集合时，不能使用其修改集合相关的方法。

它的add/remove/clear方法会抛出UnsupportedOperationException异常。

```
String[] str = new String[]{ "you", "wu" };
List list = Arrays.asList(str);

// 第一种情况：list.add("yu")运行时报错；
// 第二种情况：如果str[0] = "mine";, 那么list.get(0)也会随之修改；
```

> 说明：asList的返回对象是一个Arrays的内部类，并没有实现集合的修改方法。Arrays.asList体现的是适配器模式，只是转换接口，后台的数据仍是数组。

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

#### 1.1.5 修饰常量

```
final float PI = 3.14;	// 在编译阶段放入常量池（Java一项重要技术）
```

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
