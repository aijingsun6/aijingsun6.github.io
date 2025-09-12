# asynchronous iterator (异步迭代器)

### 1.定义
一个实现了 __aiter__() 和 __anext__() 方法的对象。

__anext__() 必须返回一个 awaitable 对象。 async for 会处理异步迭代器的 __anext__() 方法所返回的可等待对象直到其引发一个 StopAsyncIteration 异常。 由 [PEP 492](https://peps.python.org/pep-0492/) 引入


### 2.常见用法(async for)

```
async for TARGET in ITER:
    SUITE
else:
    SUITE2
```
等价于
```
iter = (ITER)
iter = type(iter).__aiter__(iter)
running = True

while running:
    try:
        TARGET = await type(iter).__anext__(iter)
    except StopAsyncIteration:
        running = False
    else:
        SUITE
else:
    SUITE2
```
### 3.example

```
import asyncio
import logging
import sys
logging.basicConfig(stream=sys.stdout,level=logging.INFO, format='%(asctime)s %(threadName)s %(taskName)s [%(levelname)s] - %(message)s')


class AsyncIterable:
    data: list
    cursor: int

    def __init__(self, size):
        self.data = list(range(size))
        self.cursor = 0

    def __aiter__(self):
        logging.info('__aiter__ called.')
        self.cursor = 0
        return self

    async def __anext__(self):
        logging.info(f'__anext__ called, cursor={self.cursor}')
        if self.cursor < len(self.data):
            data = self.data[self.cursor]
            self.cursor += 1
            return data
        else:
            raise StopAsyncIteration

async def hello():
    # 必须要放在携程函数里
    iterator = AsyncIterable(5)
    async for e in iterator:
        logging.info(e)

asyncio.run(hello())




```
output:
```
2025-09-12 08:39:29,199 MainThread Task-1 [INFO] - __aiter__ called.
2025-09-12 08:39:29,199 MainThread Task-1 [INFO] - __anext__ called, cursor=0
2025-09-12 08:39:29,199 MainThread Task-1 [INFO] - 0
2025-09-12 08:39:29,199 MainThread Task-1 [INFO] - __anext__ called, cursor=1
2025-09-12 08:39:29,199 MainThread Task-1 [INFO] - 1
2025-09-12 08:39:29,199 MainThread Task-1 [INFO] - __anext__ called, cursor=2
2025-09-12 08:39:29,199 MainThread Task-1 [INFO] - 2
2025-09-12 08:39:29,199 MainThread Task-1 [INFO] - __anext__ called, cursor=3
2025-09-12 08:39:29,199 MainThread Task-1 [INFO] - 3
2025-09-12 08:39:29,199 MainThread Task-1 [INFO] - __anext__ called, cursor=4
2025-09-12 08:39:29,199 MainThread Task-1 [INFO] - 4
2025-09-12 08:39:29,199 MainThread Task-1 [INFO] - __anext__ called, cursor=5
```