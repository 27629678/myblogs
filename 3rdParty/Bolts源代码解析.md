##1、线程的同步执行

###·问题引入

Objective-C程序支持多线程执行环境，这就意味着多个线程可能同时修改同一个对象引发严重的问题。为了避免这种多个线程同时执行同一段代码的情况，Objectvie-C引入了`@synchronized()`指令。

`@synchronized()`指令通过对一段代码进行加锁来达到试图执行该段代码的线程都会被阻塞，走到加锁的线程退出代码片段的执行，也就是`@synchronized()`内最后一条表达式执行完毕的时候。

~~~
- (void)doSomeCriticalThing
{
	@synchronized(semaphore) {
		// do some critical things
		...
	}
}
~~~

###· Objective-C中锁机制的使用

- NSLock:lock|unlock|tryLock|lockBeforeDate:等方法
- **synchronized**关键字
- pthread_mutex_t实现的锁
- GCD实现的锁（信号量）

有如下一个对象，其下有两个方法的执行是互斥的：

~~~
@implementation object

- (void)function1
{
	NSLog(@"running function1.");
}

- (void)function2
{
	NSLog(@"running function2.");
}

@end
~~~

####1、NSLock

~~~
id obj = [object new];
NSLock* lock = [NSLock new];

// queue 1
dispatch_async(queue, ^{
	sleep(1);
	[lock lock];
	[obj function1];
	sleep(10);
	[lock unlock];
});

// queue 2
dispatch_async(queue, ^{
	sleep(1);
	[lock lock];
	[obj function2];
	[lock unlock];
});
~~~

`tryLock`方法尝试加锁，若锁不可用并不阻塞线程直接返回**NO**, `lockBeforeDate:`方法尝试在指定Date之前加锁，若直至超时锁依然不可用直接返回**NO**。

####2、synchronized指令

~~~
id obj = [object new];

// queue 1
dispatch_async(queue, ^{
	@synchronized(obj) {
		[obj function1];
		sleep(10);
	}
});

// queue 2
dispatch_async(queue, ^{
	@synchronized(obj) {
		[obj function2];
	}
});
~~~

####3、pthread_mutex_t

~~~
#include "pthread.h"

id obj = [object new];
__block pthread_mutex_t mutex;

// queue 1
dispatch_async(queue, ^{
	sleep(1);
	pthread_mutex_lock(&mutex);
	[obj function1];
	sleep(10);
	pthread_mutex_unlock(&mutex);
});

// queue 2
dispatch_async(queue, ^{
	sleep(1);
	pthread_mutex_lock(&mutex);
	[obj function2];
	pthread_mutex_unlock(&mutex);
});
~~~

####4、GCD基于记数为1的信号量

~~~
#include "pthread.h"

id obj = [object new];
dispatch_semaphore_t semaphore = dispatch_semaphore_create(1);

// queue 1
dispatch_async(queue, ^{
	sleep(1);
	dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
	[obj function1];
	sleep(10);
	dispatch_semaphore_signal(semaphore);
});

// queue 2
dispatch_async(queue, ^{
	sleep(1);
	dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
	[obj function2];
	dispatch_semaphore_signal(semaphore);
});
~~~