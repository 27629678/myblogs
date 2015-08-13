###funny things about shell

----

1. 变量赋值时，`=`两边不允许含有空白字符
2. 测试命令，` [ $1 -ne $2 ] `的`[`和`]`两边的空格必须保留
3. 变量替换时，`"$1"`和`'$1'`输出结果不一样
4. 命令分隔符`;`，用于分开多个命令，例如：`clear;ls`
5. 输出重定向，`>&2`和`>&1`分别对应标准错误输出和标准正常输出，会经常用到
6. root用户判断，`if [ $(id -u) = "0" ]; then echo "superuser" fi`
7. shell中的switch, `case in`语法中的`;;`相当于C语言中的`break`
8. 奇葩的循环体，`until condition; do ... done`可以用来做菜单



[click here](http://linuxcommand.org/writing_shell_scripts.php "官网") , there are many more interesting things about shell