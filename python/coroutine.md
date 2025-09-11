# coroutine 携程

官方解释：
协程是子例程的更一般形式。 子例程可以在某一点进入并在另一点退出。 协程则可以在许多不同的点上进入、退出和恢复。 它们可通过 async def 语句来实现。 参见 [PEP 492](https://peps.python.org/pep-0492/)。


### 1. 携程的定义

一般使用 async def 语句定义携程

```
import logging
import sys
import asyncio
logging.basicConfig(stream=sys.stdout,level=logging.INFO, format='%(asctime)s %(threadName)s %(taskName)s [%(levelname)s] - %(message)s')


async def hello_coroutine(ret):
    logging.info('before sleep')
    await asyncio.sleep(1)
    logging.info('after sleep')
    return ret

cor = hello_coroutine(123)
logging.info(f'hello_coroutine is iscoroutinefunction: {asyncio.iscoroutinefunction(hello_coroutine)}')
logging.info(f'hello_coroutine() is iscoroutine: {asyncio.iscoroutine(cor)}')
logging.info(f'object:{cor}, class: {type(cor)}')

res = asyncio.run(cor)
logging.info(f'result: {res}')
```
output
```
2025-09-11 08:45:50,370 MainThread None [INFO] - hello_coroutine is iscoroutinefunction: True
2025-09-11 08:45:50,370 MainThread None [INFO] - hello_coroutine() is iscoroutine: True
2025-09-11 08:45:50,371 MainThread None [INFO] - object:<coroutine object hello_coroutine at 0x0000024136E05300>, class: <class 'coroutine'>
2025-09-11 08:45:50,371 MainThread Task-1 [INFO] - before sleep
2025-09-11 08:45:51,379 MainThread Task-1 [INFO] - after sleep
2025-09-11 08:45:51,381 MainThread None [INFO] - result: 123
```

### 2. 多携程执行
```
async def delay_print(value, sleep):
    await asyncio.sleep(sleep)
    logging.info(value)


async def multi_print():
    await asyncio.gather(delay_print('1',1),delay_print('2',2),delay_print('3',3))

asyncio.run(multi_print())
```
output
```
2025-09-11 08:51:06,991 MainThread Task-5 [INFO] - 1
2025-09-11 08:51:07,983 MainThread Task-6 [INFO] - 2
2025-09-11 08:51:08,986 MainThread Task-7 [INFO] - 3
```
从中我们可以知道：
1. 携程任务在不同的携程中执行
2. 但是在同一个thread中执行,所以携程是轻量级线程(thread)

### 3. 携程如何控制超时
1. asyncio.wait_for
2. with asyncio.timeout
##### 3.1 asyncio.wait_for
```
async def timeout_print(value, sleep):
    logging.info(f'before print {value}')
    await asyncio.sleep(sleep)
    logging.info(f'after print {value}') # will not print


async def timeout_demo():
    try:
        await asyncio.wait_for(timeout_print('value', 2), timeout=1.0)
    except asyncio.TimeoutError:
        logging.info('timeout!')

asyncio.run(timeout_demo())
```
output
```
2025-09-11 08:58:58,350 MainThread Task-1 [INFO] - before print value
2025-09-11 08:58:59,359 MainThread Task-1 [INFO] - timeout!
```

##### 3.2 with asyncio.timeout

```
async def timeout_print(value, sleep):
    logging.info(f'before print {value}')
    await asyncio.sleep(sleep)
    logging.info(f'after print {value}') # will not print

async def timeout_demo_2():
    try:
        async with asyncio.timeout(1.0):
            await timeout_print('value', 2)
    except asyncio.TimeoutError:
        logging.info('timeout!')
asyncio.run(timeout_demo_2())
```
output:
```
2025-09-11 09:01:01,803 MainThread Task-1 [INFO] - before print value
2025-09-11 09:01:02,812 MainThread Task-1 [INFO] - timeout!
```

### 4. 携程的优点
1. Efficiency: Avoid blocking I/O operations (e.g., network requests, file reads).
2. Concurrency: Handle thousands of connections simultaneously (like web servers).
3. Cleaner Code: Simplified syntax compared to callbacks or threads.

### 5. 携程 VS 线程
1. No GIL Limitation: Coroutines are single-threaded and avoid GIL bottlenecks.
2. Explicit Switching: Tasks yield control explicitly with await, unlike preemptive threading.