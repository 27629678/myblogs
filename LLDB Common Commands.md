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

显示已经设置的断点：

```
(lldb) breakpoint list
Current breakpoints:
1: name = 'alignLeftEdges:', locations = 1, resolved = 1
  1.1: where = Sketch`-[SKTGraphicView alignLeftEdges:] + 33 at /Projects/Sketch/SKTGraphicView.m:1405, address = 0x0000000100010d5b, resolved, hit count = 0
```

删除和禁用指定的断点：

```
(lldb) breakpoint [delete|disable] <breakpt-id>
```

###2、设置WatchPoint

>NOTE:与BreakPoint类似，使用`help [command] <subcommand>`查看相关的指令文档

###3、加载可执行文件

加载：

```
$ lldb /Projects/Sketch/build/Debug/Sketch.app 
$ lldb 
(lldb) file /Projects/Sketch/build/Debug/Sketch.app
```

运行：

```
(lldb) process launch 
(lldb) run 
(lldb) r
```

```
// pid
(lldb) process attach --pid 123 

// process name
(lldb) process attach --name Sketch 

// wait for process to show up
(lldb) process attach --name Sketch --waitfor
```

控制：

```
(lldb) thread continue	  // stop until hid the breakpt
(lldb) thread step-in    // The same as gdb's "step" or "s" 
(lldb) thread step-over  // The same as gdb's "next" or "n"
(lldb) thread step-out   // The same as gdb's "finish" or "f"
(lldb) thread until 100	  // run to line 100
```

执行表达式：

```
(lldb) expr (int) printf ("I have a pointer 0x%llx.\n", self)
```