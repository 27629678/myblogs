###tcpdump命令简介

####一、description

它就是一个截包工具，与`wireshark`不同，`tcpdump`没有**GUI**，只能在命令行下使用，却换来了使用上的灵活性，这也是命令行一贯吹嘘自己牛逼的地方。

####二、common arguments

- w(not W) flag, [-w file]:保存截获的包写入指定的文件；
- r flag, [-r file]:从指定的文件而不**[network interface]**读取截获的包的数据；
- -V(not v) flag, [-V files]:与-r类似，-V是从多个文件读数据；
- -A flag, 以ASCII码的方式输出包内容（不包含链路层的头部分）；
- -c(not C) flag, [-c count]:当截获指定**count**的包后退出程序；
- -D(--list-interfaces)：列出当前系统中可以被用来截包的网络接口号及名字；这个参数在没有命令可以列出网络接口的操作系统中非常有用，例如`Windows systems`，或者没有`ifconfig -a`命令的**Unix**操作系统；除此之外，该参数在旧版本的`tcpdump`程序中不起作用的，因为在`libpcap`库中没有**pcap_findalldevs()**方法； 
- -i:监听指定的网络接口；
- -Q[ -Q packet-metadata-filter ]：指定基于网络接口或者进程无数据信息的filter表达式；