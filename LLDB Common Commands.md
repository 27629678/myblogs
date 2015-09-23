##·LLDB常用命令

###1、设置断点

#####- 指定文件名与行号

```
(lldb) breakpoint set --file foo.c --line 12 
(lldb) breakpoint set -f foo.c -l 12
```

####- 指定方法名字

C/C++方法：

```
// name
(lldb) breakpoint set --name foo 
(lldb) breakpoint set -n foo

// method
(lldb) breakpoint set --method foo 
(lldb) breakpoint set -M foo
```

Objective-C方法：

```
// name
(lldb) breakpoint set -n "-[MailMainViewController viewWillFirstAppear:]" 
(lldb) br s -n "-[MailMainViewController viewWillFirstAppear:]"

// selector
(lldb) breakpoint set --selector alignLeftEdges: 
(lldb) breakpoint set -S alignLeftEdges:
```

设置与取消别名：

```
// alias
(lldb) command alias bfl breakpoint set -f %1 -l %2 
(lldb) bfl foo.c 12

// unalias
(lldb) command unalias b
```