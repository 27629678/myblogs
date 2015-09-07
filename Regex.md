###元字符

- \b, 代表着单词的开始或者结束，常用作单词的分界间隔
- ., 匹配除换行符以外的任意字符
- *, 表示\*前面的内容可以重复出现多次
- \d, 用于匹配一位数字[0-9], eg:\d{2,3}, 2-3位数字
- \s, 匹配任意的空白字符，包括空格、制表符、换行符及中文的空格
- \w, 字母、数字、下划线或汉字等
- ^, 匹配字符串的开始
- $, 匹配字符串的结束

###要去条件

- “|”把不同的规则连接在一起用于匹配

###分组(Capture Group)or(Capturing Group)

使用小括号指定一个子表达式后，匹配这个子表达式的文本(也就是此分组捕获的内容)可以在表达式或其它程序中作进一步的处理。默认情况下，每个分组会自动拥有一个组号，规则是：从左向右，以分组的左括号为标志，第一个出现的分组的组号为1，第二个为2，以此类推。