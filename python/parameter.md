# parameter (形参)

### 1.定义
function/method 定义中的命名实体，它指定函数可以接受的一个 argument(参数)

### 2.分类
有五种形参

### positional-or-keyword(位置或关键字)
指定一个可以作为 位置参数 传入也可以作为 关键字参数 传入的实参。这是默认的形参类型
```
def func(foo, bar=None): ...

eg:
func(2, bar=1)
foo=2
bar=1
```
##### 2.2 positional-only(仅限位置)
指定一个只能通过位置传入的参数。 仅限位置形参可通过在函数定义的形参列表中它们之后包含一个 / 字符来定义

```
def func(foo, bar, /, positional_or_keyword): ...

eg:
func(1,2)
foo=1
bar=2

```


##### 2.3 keyword-only(仅限关键字)

指定一个只能通过关键字传入的参数。仅限关键字形参可通过在函数定义的形参列表中包含单个可变位置形参或者在多个可变位置形参之前放一个 * 来定义

```
def func(arg, *, foo, bar):

eg:
fun(1,2,3)
arg=1
foo=2
bar=3
```

##### 2.4 var-positional(可变位置)
指定可以提供由一个任意数量的位置参数构成的序列（附加在其他形参已接受的位置参数之后）。这种形参可通过在形参名称前加缀 * 来定义

```
def func(*args):...
eg:

def var_positional(*args):
    print(args)

var_positional(1,2,3,4,5)
output:
(1, 2, 3, 4, 5)
```

##### 2.5 var-keyword(可变关键字)

指定可以提供任意数量的关键字参数（附加在其他形参已接受的关键字参数之后）。这种形参可通过在形参名称前加缀 ** 来定义

```
def func(**kwargs):...
eg:

def var_keyword(**kwargs):
    print(kwargs)

var_keyword(foo=1,bar=2)
output:
{'foo': 1, 'bar': 2}
```

### 3.parameter (形参) vs argument(参数)

说到 parameter (形参),不得不提到argument(参数),一般称argument(参数)为实参

形参 是由出现在函数定义中的名称来定义的，而 参数 则是在调用函数时实际传入的值。 形参定义了一个函数能接受什么 参数种类


### 4. 总结

python相对其他语言,传参更加灵活
