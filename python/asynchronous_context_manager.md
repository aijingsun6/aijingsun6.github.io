# asynchronous context manager

此种对象通过定义 __aenter__() 和 __aexit__() 方法来对 async with 语句中的环境进行控制。 由 [PEP 492](https://peps.python.org/pep-0492/) 引入。


### 语法
```
async with EXPR as VAR:
    BLOCK
```

展开为
```
mgr = (EXPR)
aexit = type(mgr).__aexit__
aenter = type(mgr).__aenter__

VAR = await aenter(mgr)
try:
    BLOCK
except:
    if not await aexit(mgr, *sys.exc_info()):
        raise
else:
    await aexit(mgr, None, None, None)
```

### example
```
import asyncio
import logging
import sys
logging.basicConfig(stream=sys.stdout,level=logging.INFO, format='%(asctime)s %(threadName)s %(taskName)s [%(levelname)s] - %(message)s')

async def sleep_print(value=None,secs=1):
    await asyncio.sleep(secs)
    return value

class AsyncContextManager:

    def __init__(self,value='AsyncContextManager'):
        self.value = value

    async def __aenter__(self,):
        logging.info('__aenter__')
        return await sleep_print(self.value)

    async def __aexit__(self, exc_type, exc, tb):
        logging.info(f'__aexit__ {exc_type} {exc} {tb}')
        await sleep_print('__aexit__')

async def hello():
    async with AsyncContextManager() as acm:
        logging.info(f'with {acm}')

asyncio.run(hello())
```

output
```
2025-09-15 08:50:10,728 MainThread Task-1 [INFO] - __aenter__
2025-09-15 08:50:11,733 MainThread Task-1 [INFO] - with AsyncContextManager
2025-09-15 08:50:11,733 MainThread Task-1 [INFO] - __aexit__ None None None
```