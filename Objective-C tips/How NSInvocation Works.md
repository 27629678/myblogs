###How NSInvocation Works

----

#### Overview

NSInvocation是一个静态配置的Objective-C消息，任何时候调用方法或者访问类的属性都会发送一个消息给这个类或者类的实例。

	```
	NSMutableArray *items = [NSMutableArray array];

    NSNumber *item = @(2);
    NSMethodSignature* signature = [NSMutableArray instanceMethodSignatureForSelector:@selector(addObject:)];
    NSInvocation* aInvocation = [NSInvocation invocationWithMethodSignature:signature];
    [aInvocation setTarget:_items];
    [aInvocation setSelector:@selector(addObject:)];
    [aInvocation setArgument:&item atIndex:2];
    [aInvocation invoke];

    Warrior *awarrior = [[Warrior alloc] init];

    signature = [Warrior instanceMethodSignatureForSelector:@selector(description)];
    aInvocation = [NSInvocation invocationWithMethodSignature:signature];
    [aInvocation setTarget:awarrior];
    [aInvocation setSelector:@selector(description)];
    [aInvocation invoke];

    NSString* msg = nil;
    [aInvocation getReturnValue:&msg];
	```
