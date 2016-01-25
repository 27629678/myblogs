###UTF-8

UTF-8是unicode编码方式中的一种，使用1-6个字节长度，所以它是`长度可变的编码方式`，第一个字节与ASCII码兼容，2003年重新修订为RFC3629规范。

编码方式如下：

![coding_image](/Users/hzyuxiaohua/projects/markdown/Resources/utf8_coding_image.png)

优点：

- 完全兼容ASCII（U+0000到U+007F）码；
- 这种编码方式保证了一个字符的序列不会包括其它字符的序列；