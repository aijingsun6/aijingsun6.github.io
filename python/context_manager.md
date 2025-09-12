# context manager (上下文管理器)

### 1. 定义
在 with 语句中通过定义 __enter__() 和 __exit__() 方法来控制环境状态的对象。 参见 [PEP 343](https://peps.python.org/pep-0343/)。


### 2. 常见语法(with)

```
with EXPRESSION as (TARGET):
    SUITE
```
等价于
```
manager = (EXPRESSION)
enter = type(manager).__enter__
exit = type(manager).__exit__
value = enter(manager)

try:
    TARGET = value
    SUITE
except:
    if not exit(manager, *sys.exc_info()):
        raise
else:
    exit(manager, None, None, None)
```
注意:
1. TARGET 只与 __enter__ 返回的对象有关
2. __exit__ 永远调用的是 manager的方法


复合用法:
```
with A() as a, B() as b:
    SUITE


```
等价于
```
with A() as a:
    with B() as b:
        SUITE
```
比如:

```
with locked(myLock):
  ...


with open(...) as f:
   ...
```


### 3. example

```
import logging
import sys
logging.basicConfig(stream=sys.stdout,level=logging.INFO, format='%(asctime)s %(threadName)s %(taskName)s [%(levelname)s] - %(message)s')


class CMDemo(object):
    name:str
    def __init__(self,name):
        self.name = name

    def __enter__(self):
        logging.info('CMDemo.__enter__ called')
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        logging.info(f'CMDemo.__exit__,{exc_type}, {exc_val}, {exc_tb}')
        pass

    def hello(self):
        logging.info(f'CMDemo.hello: {self.name}')

with CMDemo('cm') as cm:
    cm.hello()


class CMDemo2(object):
    cm: CMDemo
    def __init__(self, name):
        self.cm = CMDemo(name)

    def __enter__(self):
        logging.info('CMDemo2.__enter__ called')
        return self.cm

    def __exit__(self, exc_type, exc_val, exc_tb):
        logging.info(f'CMDemo2.__exit__,{exc_type}, {exc_val}, {exc_tb}')
        pass

    def hello(self):
        logging.info(f'CMDemo2.hello: {self.cm.name}')

with CMDemo2('222') as cm:
    logging.info(f'{type(cm)}')
    cm.hello()
```
output:
```
2025-09-12 08:59:14,125 MainThread None [INFO] - CMDemo.__enter__ called
2025-09-12 08:59:14,125 MainThread None [INFO] - CMDemo.hello: cm
2025-09-12 08:59:14,125 MainThread None [INFO] - CMDemo.__exit__,None, None, None
2025-09-12 08:59:14,125 MainThread None [INFO] - CMDemo2.__enter__ called
2025-09-12 08:59:14,125 MainThread None [INFO] - <class '__main__.CMDemo'>
2025-09-12 08:59:14,125 MainThread None [INFO] - CMDemo.hello: 222
2025-09-12 08:59:14,125 MainThread None [INFO] - CMDemo2.__exit__,None, None, None
```

含有异常的使用
```
with CMDemo('cm') as cm:
    raise Exception('xyz')
```

output:
```
2025-09-12 09:04:01,796 MainThread None [INFO] - CMDemo.__enter__ called
2025-09-12 09:04:01,796 MainThread None [INFO] - CMDemo.__exit__,<class 'Exception'>, xyz, <traceback object at 0x00000269E81D9440>
Traceback (most recent call last):
  File "D:\github\python-example\context_manager.py", line 26, in <module>
    raise Exception('xyz')
Exception: xyz
```