# annotation (标注)

### 1. 定义
关联到某个变量、类属性、函数形参或返回值的标签，被约定作为 类型注解 来使用。

局部变量的标注在运行时不可访问，但全局变量、类属性和函数的标注会分别存放模块、类和函数的 __annotations__ 特殊属性中。


### 2.分类
#### 2.1 function annotation （函数标注）

即针对函数形参或返回值的 annotation

```
def sum_two_numbers(a: int, b: int) -> int:
   return a + b
print(inspect.get_annotations(sum_two_numbers))

def hello(a:int):
    print(a)
print(inspect.get_annotations(hello))

def hello_2(a:int,b: tuple[str,int]) -> list[str]:
    return []
d = inspect.get_annotations(hello_2)
for k,v in d.items():
    print(k,type(k), v, type(v))

output:
{'a': <class 'int'>, 'b': <class 'int'>, 'return': <class 'int'>}

{'a': <class 'int'>} # 没有return信息

a <class 'str'> <class 'int'> <class 'type'>
b <class 'str'> tuple[str, int] <class 'types.GenericAlias'>
return <class 'str'> list[str] <class 'types.GenericAlias'>
```

#### 2.2 variable annotation （变量标注）

对变量或类属性的 annotation。

在标注变量或类属性时，还可选择为其赋值:
```
class C:
    field_a: str
    field_b: int
    field_c: object
    field_d: any
    field_e: None

print(inspect.get_annotations(C))

output:

{'field_a': <class 'str'>, 'field_b': <class 'int'>, 'field_c': <class 'object'>, 'field_d': <built-in function any>, 'field_e': None}
```

#### 2.3 module variable annotation (模块变量标注)

```
# my_module.py

NAME: str = 'NAME'
AGE: int = 10

# main.py

import my_module
import inspect

print(inspect.get_annotations(my_module))

output:
{'NAME': <class 'str'>, 'AGE': <class 'int'>}

```

### 参考文档：
- [Type hints cheat sheet](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html)
- [Specification for the Python type system](https://typing.python.org/en/latest/spec/index.html)
- [Static Typing with Python](https://typing.python.org/en/latest/)
- [PEP 484 – Type Hints](https://peps.python.org/pep-0484/)
- [PEP 526 – Syntax for Variable Annotations](https://peps.python.org/pep-0526/)
- [PEP 3107 – Function Annotations](https://peps.python.org/pep-3107/)
- [Annotations Best Practices](https://docs.python.org/3/howto/annotations.html)
