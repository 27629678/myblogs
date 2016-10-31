## 0x01 Decorator

装饰符，在Python2.4引入的概念，在不使用继承的情况下实现功能的扩展，参考AOP编程的实现；

```
List = {}

def route(rule):
    def __decorator (f):
        List['key'] = rule
        # do something
        return f

    return __decorator

@route('/index')
def login(user):
    print(user + ' login...')

# 与@route('/index')功能一致
# route('/index')(login)

if __name__ == '__main__':
    print(List)
    print login.__name__
```