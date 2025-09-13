# generator(生成器)
### 定义
返回一个 generator iterator 的函数。它看起来很像普通函数，不同点在于其包含 yield 表达式以便产生一系列值供给 for-循环使用或是通过 next() 函数逐一获取。

通常是指生成器函数，但在某些情况下也可能是指 生成器迭代器。如果需要清楚表达具体含义，请使用全称以避免歧义。

没有明确说明:一般生成器是指: 生成器函数


### 种类:
- generator(生成器)
- generator iterator (生成器迭代器)
- asynchronous generator(异步生成器)
- asynchronous generator iterator -- 异步生成器迭代器

### 最简单的生成器函数

```
SIZE = 5
def gen():
    for i in range(SIZE):
        yield i
g = gen()
print(type(gen)) # <class 'function'>
print(type(g))   # <class 'generator'>

for e in gen():
    print(e)
等价于:
while True:
    try:
        e = next(g)
        print(e)
    except StopIteration:
        break
```
output
```
<class 'function'>
<class 'generator'>
0
1
2
3
4
0
1
2
3
4
```
### generator iterator -- 生成器迭代器
generator 函数所创建的对象。

每个 yield 会临时暂停处理，记住当前位置执行状态（包括局部变量和挂起的 try 语句）。当该 生成器迭代器 恢复时，它会从离开位置继续执行（这与每次调用都从新开始的普通函数差别很大）。


