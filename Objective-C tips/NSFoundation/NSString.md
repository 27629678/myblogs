## NSString.length的基本原理

> NOTE: length的值为字符串UTF16编码**码元（code unit）**的个数

![emoji](/home/hzyuxiaohua/projects/markdowns/Resources/emoji-family-man-man-girl-boy.png)

对，就是上面这个`emoji`表情，string.length = 11; 非常惊讶！

从常识来讲，常见的单个**可见字符**有长度为1，2，3，4，其中长度为2，3，4的emoji表情居多，再有就是罕见东方文字；

但是，这个表情居然长度为11，太神奇了；

首先，看一下这个字符的UTF8编码：

```
F09F91A8 E2808DF0 9F91A9E2 808DF09F 91A7E280 8DF09F91 A6
```
可以看到以上25个字节的内容，按照UTF8编码，可以拆分为7个字符，如下所示：

```
\xF09F91A8
\xE2808D
\xF09F91A9
\xE2808D
\xF09F91A7
\xE2808D
\xF09F91A6
```

由些可见这个emoji表情由**\xF09F91A8**、**\xF09F91A9**、**\xF09F91A7**和**\xF09F91A6**四个表情组合而成的（的确是这样，不再验证），即表情中从左到右，从上到下分别自己表情组合而成；中间的**\xE2808D**是联字符号

看到这里，应该明白了，其实这是一个组合字符串，经过Unicode解码之后显示一个表情符号，这样再经过转码为UTF16除码元（WORD）得到的长度为11就不足为奇了；