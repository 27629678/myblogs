###1、自执行函数

代码形式如下：

```
(function() { code here })()
```

\- 优点：pass

\- 缺点：pass


###2、内建对象**arguments**

js的方法有一个叫作**arguments**的内建的对象，该对象用于保存方法被调用（called or invoked）时传递进来的参数。

```
function js_log() {
	var items = Array.prototype.slice.call(arguments)
	console.log(items)
}

(function() {
	js_log('1','2')
})()
```

执行以上脚本输出如下信息：

```
[ '1', '2' ]
```