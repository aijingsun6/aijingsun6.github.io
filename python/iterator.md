# iterator(迭代器)

先看官方的解释：

用来表示一连串数据流的对象。 重复调用迭代器的 __next__() 方法 (或将其传给内置函数 next()) 将逐个返回流中的项。 当没有数据可用时则将引发 StopIteration 异常。 到这时迭代器对象中的数据项已耗尽，继续调用其 __next__() 方法只会再次引发 StopIteration。 迭代器必须具有 __iter__() 方法用来返回该迭代器对象自身，因此迭代器必定也是可迭代对象，可被用于其他可迭代对象适用的大部分场合。 一个显著的例外是那些会多次重复访问迭代项的代码。 容器对象 (例如 list) 在你每次将其传入 iter() 函数或是在 for 循环中使用时都会产生一个新的迭代器。 如果在此情况下你尝试用迭代器则会返回在之前迭代过程中被耗尽的同一迭代器对象，使其看起来就像是一个空容器。

条件如下：
1. 是一个对象
2. 必须实现 迭代器 协议(protocol)
3. __next__ 结束标志是raise StopIteration

```
interface iterator {
    __next__()
    __iter__()
}
```

### 最简单的迭代器
```
a = [1,2,3]
print(type(a))
b = iter(a) # return a iterator
print(type(b))
for e in b:  # for syntax
  print(e)
# output
<class 'list'>
<class 'list_iterator'>
1
2
3    
```

### 稍微复杂的例子
```
class MyIterator:
    data: list
    cursor:int
    def __init__(self, values:list):
        self.data = values
        self.cursor = 0

    def __iter__(self):
        print('__iter__ called')
        self.cursor = 0
        return self

    def __next__(self):
        print('__next__ called')
        if self.cursor < len(self.data):
            value = self.data[self.cursor]
            self.cursor += 1
            return value
        raise StopIteration()
it = MyIterator([1,2,3])
    for e in it:
        print(e)
# output
__iter__ called # return a iterator
__next__ called
1
__next__ called
2
__next__ called
3
__next__ called
raise StopIteration # stoped
```

### 迭代器的一般展开
```
its = iter(it)
while True:
  try:
    e = next(its)
    print(e) # do something with element
  except StopIteration as e:
    break # stop
```

### 注意
迭代器不是线程安全的

### 总结
迭代器是最基础的数据类型