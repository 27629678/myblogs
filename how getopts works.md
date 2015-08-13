###small getopts turorial

-----

当你解析命令行参数时，`getopts`是一个不错的选择；不像他的兄弟`getopt`（注意没有s），它是一个bash内建的命令行工具，其优势如下：

- 无需处理参数的位置
- 轻松的设置shell变量
- 无需担心getopt处理参数时的bug（例如空格的处理）
- getopts遵循POSIX标准

***P.S.:getopts不能解析GNU类型的长选项（--options）或者XF-86类型的选项（-options)***

####Description

1. **Terminology**

	很有必要知道我们正在讨论什么，考虑下面的命令行:

	```
	mybackup -x -f /etc/mybackup.conf -r ./foo.txt ./bar.txt

	```

	以上命令行是基于位置的参数，我们可以把它解析成小块的逻辑信息如下:

	- -x是一个**Option**，或者是一个标记，也有可能是一个开关
	- -f也是一个**Option**，但是这个option有一个**additional argument**:*/etc/mybackup.conf*
	- -r依赖不同的配置，该处是一个选项，不带有任何额外参数
	- ./foo.txt和./bar.txt依然是参数，没有任何相关的选项，称之为**mass-arguments**

	为了说明getopts的优势，它可以这样来**Read**这条命令行：

	```
	mybackup -xrf /etc/mybackup.conf ./foo.txt ./bar.txt

	```
	
2. **How is works**

	由于getopts当没有参数可以解析是可以设置解析状态为***False***，这使得可以很方便的在while循环中实现全部参数的解析：
	
	```
	while getopts...; do
		...
	done
	```
	
	getopts尽可能的解析选项和参数，当遇到第一个非选项参数（一个以‘-’开头的字符串，或者结束符‘--’）时停止。
	
3. **Used variables**

	- OPTIND：保存下一个将要被处理的参数的索引。这就是在调用之间getopts如何记住自身的状态的原因，对于shift之后，getopts继续处理也是很有用的。该值初始化为1，**当若重新解析时，要重新设置为1**
	- OPTARG：用于保存getopts找到的选项对应的参数，也用于保存未知选项的**flag**
	- OPTERR：值为0或1，用于指示getopts是否需要显示错误日志，当shell启动的时候，该值初始化为1；**若不想看到烦人的日志可以将该值设置成0**
	
4. **Specify what you want**

	getopts基本方法如下：
	
	```
	getopts OPTSTRING VARNAME [ARGS...]
	```
	
	术语解释如下：
	
	- OPTSTRING：将要被解析的选项和参数
	- VARNAME:shell变量对应的选项
	- ARGS：解析这些选项用于替换基于位置的选项解析

	**The Option String**
	
	选项字符串用于告诉解释器要查找哪些选项，哪些选项带有参数。假如要查找选项-f、-A和-x,语法非常简单，如下所示：
	
	```
	getopts fAx VARNAME
	```
	
	当某个选项后面需要带有参数时，只需要在对应的选项后面添加':'即可。假如选项A后面要带有参数，语法如下所示：
	
	```
	getopts fA:x VARNAME
	```
	
	**Error Reporting**
	
	getopts有两种模式输出错误日志：
	
	- verbose mode
	- silent mode

	在示例代码中，我们将介绍如何默认使用silent mode。

####Using it

1. A first example
	
	一个非常简单的例子，只有一个选项可以输入（-a），并使用添加‘:’到**Option String**前来禁用错误日志输出的verbose mode。代码如下：
	
	```
	#! /bin/bash
	
	while getopts ":a" opt; do
		case $opt in
			a)
				echo "-a was triggered!" >&2
				;;
			\?)
				echo "Invalid option:-$OPTARG" >&2
				;;
		esac
	done
	```
	
2. An option with argument

	```
	#!/bin/bash
 
	while getopts ":a:" opt; do
  		case $opt in
    		a)
      			echo "-a was triggered, Parameter: $OPTARG" >&2
      			;;
    		\?)
      			echo "Invalid option: -$OPTARG" >&2
      			exit 1
      			;;
    		:)
      			echo "Option -$OPTARG requires an argument." >&2
      			exit 1
      			;;
  		esac
	done
	```