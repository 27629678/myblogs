## Parser for command-line options, arguments and sub-commands

### 0x00 prolog

[2.7.12官方文档](https://docs.python.org/2/library/argparse.html)

### 0x01 example

```
import argparse


parser = argparse.ArgumentParser()
parser.add_argument('--foo', help='foo argument description')


args = parser.parse_args()
print args.foo
```

### 0x02 creation

```
// add description
parser = argparse.ArgumentParser(description='programme can do something description')
```

`ArgumentParser`常用参数：

- prog - 程序的名字，默认值是`sys.argv[0]`;
- description - 程序的简介，默认值是从添加的参数中自动生成;
- parents - 从其它`ArgumentParser`对象中‘继承’参数列表；
- argument_default - 参数的全局默认值，未指定的情况下默认值`None`；

```
parser = argparse.ArgumentParser(argument_default=argparse.SUPPRESS)
parser.add_argument('--foo')
parser.add_argument('bar', nargs='?')
parser.parse_args(['--foo', '1', 'BAR'])
Namespace(bar='BAR', foo='1')
parser.parse_args([])
Namespace()
// 若未指定argparse.SUPPRESS，刚输出Namespace(bar=None, foo=None)
```

### 0x03 add_argument()方法

```
ArgumentParser.add_argument(name or flags...[, action][, nargs][, const][, default][, type][, choices][, required][, help][, metavar][, dest])
```

参数列表：

- name or flags - `option`参数的名字，包含`optional arguments`和`positional arguments`两种，如-f, --foo;
- action - 指定`action`的类型;
- nargs - 参数的个数（不确切这个翻译，待完善）;
- const - `action`和`nargs`所需要的一个常量;
- default - 在命令行中未指定该参数时的默认值;
- type - 参数的类型，即Shell将字符串转换成的数据类型，如`int`等;
- choices - 指定参数取值范围的容器;
- required - 该参数是否允许不指定;
- help - 该参数的简介;
- metavar - `Usage`描述中，该参数的名字;
- dest - `parse_args()`方法返回的该参数的属性名称;

#### 3.1 name or flags

`add_argument()`方法**必须（MUST）**知道该参数的类型——可选（optional）参数和位置（positional）参数。所以，第一个传入该方法的参数必须要么是一个可选参数，如-f， --foo等，或者位置参数，如bar，参数列表；

```
// optional arguments
parser.add_argument('-f', '--foo')

// positional argument
parser.add_argument('bar')
```

#### 3.2 action

`action`仅允许操作命令行参数列表中的参数，然后再通过`parse_args()`方法返回；

##### 3.2.1 store

`store` 仅保存该参数的值，是默认的`action`，如：

```
parser = argparse.ArgumentParser()
parser.add_argument('--foo')
parser.parse_args('--foo 1'.split())

Namespace(foo='1')
```

##### 3.2.2 store_const

`store_const` 这个`action`通常与`optional`参数一起使用，用于指定一个默认值(该值由`const`指定)，如：

```
parser.add_argument('--foo', action='store_const', const=42)
parser.parse_args(['--foo'])

Namespace(foo=42)
```

##### 3.2.3 store_true and store_false

这两个`action`用于指定该参数的默认值为`True`或`False`，与`store_const`类似，如：

```
parser.add_argument('--foo', action='store_true')
parser.add_argument('--bar', action='store_false')
parser.add_argument('--baz', action='store_false')
parser.parse_args('--foo --bar'.split())

Namespace(bar=False, baz=True, foo=True)
```

##### 3.2.4 append

`count`用于统计该参数在命令行中出现的次数，如：

```
parser.add_argument('--verbose', '-v', action='count')
parser.parse_args(['-vvv'])

Namespace(verbose=3)
```

##### 3.2.5 version

`version`用于输出该程序的版本号，如：

```
parser = argparse.ArgumentParser(prog='PROG')
parser.add_argument('--version', action='version', version='%(prog)s 2.0')
parser.parse_args(['--version'])

PROG 2.0
```
##### 3.2.6 override action

可以自定义`action`，推荐的方法是扩展`Action`，重写`__call__`和`__init__`方法，如：

```
>>> class FooAction(argparse.Action):
...     def __init__(self, option_strings, dest, nargs=None, **kwargs):
...         if nargs is not None:
...             raise ValueError("nargs not allowed")
...         super(FooAction, self).__init__(option_strings, dest, **kwargs)
...     def __call__(self, parser, namespace, values, option_string=None):
...         print '%r %r %r' % (namespace, values, option_string)
...         setattr(namespace, self.dest, values)
...
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', action=FooAction)
>>> parser.add_argument('bar', action=FooAction)
>>> args = parser.parse_args('1 --foo 2'.split())
Namespace(bar=None, foo=None) '1' None
Namespace(bar='1', foo=None) '2' '--foo'
>>> args

Namespace(bar='1', foo='2')
```

#### 3.3 nargs

用于指定该参数后面可以跟随多个值，支持的值如下所示：

- N (整数)，表示N个值将被收集到一个数组内，如：

```
parser.add_argument('--foo', nargs=2)
parser.add_argument('bar', nargs=1)
parser.parse_args('c --foo a b'.split())

Namespace(bar=['c'], foo=['a', 'b'])
``` 

- '?'， 接受一个命令行参数值，若没有出现该参数，则将`default`的值作为默认抛出；若指定的该参数，但是没有参数值，则将`const`的值作为默认值抛出，如下所示：

```
parser.add_argument('--foo', nargs='?', const='c', default='d')
parser.add_argument('bar', nargs='?', default='d')
parser.parse_args(['XX', '--foo', 'YY'])

Namespace(bar='XX', foo='YY')

parser.parse_args(['XX', '--foo'])
Namespace(bar='XX', foo='c')

parser.parse_args([])
Namespace(bar='d', foo='d')
```

`nargs='?'`的一个常用例子如下所示：

```
parser = argparse.ArgumentParser()
parser.add_argument('infile', nargs='?', type=argparse.FileType('r'), default=sys.stdin)
parser.add_argument('outfile', nargs='?', type=argparse.FileType('w'), default=sys.stdout)
parser.parse_args(['input.txt', 'output.txt'])
Namespace(infile=<open file 'input.txt', mode 'r' at 0x...>, outfile=<open file 'output.txt', mode 'w' at 0x...>)

parser.parse_args([])
Namespace(infile=<open file '<stdin>', mode 'r' at 0x...>, outfile=<open file '<stdout>', mode 'w' at 0x...>)
```

- '*', 收集多个参数值进入参数值数组，如：

```
parser.add_argument('--foo', nargs='*')
parser.add_argument('--bar', nargs='*')
parser.add_argument('baz', nargs='*')
parser.parse_args('a b --foo x y --bar 1 2'.split())

Namespace(bar=['1', '2'], baz=['a', 'b'], foo=['x', 'y'])
```

- '+', 与'*'类似，但是指定的值列表至少含有一个值，如：

```
parser.add_argument('foo', nargs='+')
parser.parse_args(['a', 'b'])
Namespace(foo=['a', 'b'])

parser.parse_args([])
usage: PROG [-h] foo [foo ...]
PROG: error: too few arguments
```

- argparse.REMAINDER

保存所有传入的参数列表及参数的值，通常对调用其它命令时非常有用，如：

```
parser.add_argument('--foo')
parser.add_argument('command')
parser.add_argument('args', nargs=argparse.REMAINDER)
parser.parse_args('--foo B cmd --arg1 XX ZZ'.split())

Namespace(args=['--arg1', 'XX', 'ZZ'], command='cmd', foo='B')
```

#### 3.4 const and default

前面已经讲过了，就不再总结了，略过；

#### 3.5 type

- 从命令行读入的参数字符串值转换成指定的类型

```
parser.add_argument('foo', type=int)
parser.add_argument('bar', type=file)
parser.parse_args('2 temp.txt'.split())

Namespace(bar=<open file 'temp.txt', mode 'r' at 0x...>, foo=2)
```

- FileType factory

```
parser.add_argument('bar', type=argparse.FileType('w'))
parser.parse_args(['out.txt'])

Namespace(bar=<open file 'out.txt', mode 'w' at 0x...>)
```

- 自定义方法，接受一个字符串参数并有返回值的方法

```
def perfect_square(string):
	value = int(string)
	sqrt = math.sqrt(value)
	if sqrt != int(sqrt):
		msg = "%r is not a perfect square" % string
		raise argparse.ArgumentTypeError(msg)
	
	return value
	
parser.add_argument('foo', type=perfect_square)
parser.parse_args(['9'])
Namespace(foo=9)

parser.parse_args(['7'])
usage: PROG [-h] foo
PROG: error: argument foo: '7' is not a perfect square
```

#### 3.6 choices

`choices`用于限制该参数的值是**集合**中的元素之一，若给定的参数值不是其中的元素之一，则会抛出Error；

> **NOTE：**校验传入的值是否为限制集合中的元素之一，是经过`type`转换之后的值，所以，限制集合中的元素类型也要与`type`一致；

```
parser = argparse.ArgumentParser(prog='game.py')
parser.add_argument('move', choices=['rock', 'paper', 'scissors'])
parser.parse_args(['rock'])
Namespace(move='rock')

parser.parse_args(['fire'])
usage: game.py [-h] {rock,paper,scissors}
game.py: error: argument move: invalid choice: 'fire' (choose from 'rock',
'paper', 'scissors')
```

#### 3.7 required

`required`用于指定该参数必须给定，否则抛出Error；

```
parser = argparse.ArgumentParser()
parser.add_argument('--foo', required=True)
parser.parse_args(['--foo', 'BAR'])
Namespace(foo='BAR')

parser.parse_args([])
usage: argparse.py [-h] [--foo FOO]
argparse.py: error: option --foo is required
```

#### 3.8 help

`help`用于在输出Usage时描述该参数的用途，如：

```
parser = argparse.ArgumentParser(prog='frobble')
>>> parser.add_argument('--foo', action='store_true',
                        help='foo the bars before frobbling')
>>> parser.add_argument('bar', nargs='+',
                        help='one of the bars to be frobbled')
>>> parser.parse_args(['-h'])
usage: frobble [-h] [--foo] bar [bar ...]

positional arguments:
 bar     one of the bars to be frobbled

optional arguments:
 -h, --help  show this help message and exit
 --foo   foo the bars before frobbling
```

#### 3.9 metavar

`metavar`用于生成`help`信息时该参数的名称，注意与`dest`区别，如：

```
parser = argparse.ArgumentParser()
parser.add_argument('--foo', metavar='YYY')
parser.add_argument('bar', metavar='XXX')
parser.parse_args('X --foo Y'.split())
Namespace(bar='X', foo='Y')

parser.print_help()
usage:  [-h] [--foo YYY] XXX

positional arguments:
 XXX

optional arguments:
 -h, --help  show this help message and exit
 --foo YYY
```

#### 3.10 dest

`dest`用于自定义该参数的属性名称，如：

```
parser.add_argument('--foo', dest='bar')
parser.parse_args('--foo XXX'.split())
Namespace(bar='XXX')
```

### 4 parse_args()方法

该方法的功能，就是从`sys.argv`中解析参数，并生成一个新的`Namespace`对象，包含了参数相关的属性及值；