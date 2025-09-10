# awaitable 可等待对象

一个可在 await 表达式中使用的对象。 可以是 coroutine 或是具有 __await__() 方法的对象。 参见 [PEP 492](https://peps.python.org/pep-0492/)。


In Python, an awaitable is any object that can be used in an await expression. 

Awaitables are central to asynchronous programming and are typically used to pause execution until a concurrent operation completes. 

There are three primary types of awaitable objects:

### Coroutines
Defined with async def, coroutines are the most common awaitable. When awaited, they yield control to the event loop until the operation completes.
```
import asyncio

async def my_coroutine():
    print("Start")
    await asyncio.sleep(1)  # Pause for 1 second
    print("End")
print(type(my_coroutine)) # my_coroutine is function
print(type(my_coroutine())) # my_coroutine() is coroutine
# Run the coroutine
asyncio.run(my_coroutine())
```

output

```
<class 'function'>
<class 'coroutine'>
Start
...awaitable.py:8: RuntimeWarning: coroutine 'my_coroutine' was never awaited
  print(type(my_coroutine()))
RuntimeWarning: Enable tracemalloc to get the object allocation traceback
End
```

### Task
Wrappers around coroutines that schedule them to run concurrently on the event loop. Created using asyncio.create_task().

```
import asyncio

async def my_coroutine():
    print("Start")
    await asyncio.sleep(1)  # Pause for 1 second
    print("End")

async def main():
    task = asyncio.create_task(my_coroutine())
    print(task)
    print(type(task))
    await task  # Wait for the task to complete

asyncio.run(main())
```
output
```
<Task pending name='Task-2' coro=<my_coroutine() running at D:\github\python-example\awaitable.py:3>>
<class '_asyncio.Task'>
Start
End
```

### Futures
Low-level objects representing the result of an asynchronous operation. Typically created by the event loop or frameworks. Users rarely instantiate Future directly.

```
async def main():
    loop = asyncio.get_running_loop()
    future = loop.create_future()

    # Simulate setting a result after 1 second
    async def set_result():
        print("Start")
        await asyncio.sleep(1)
        print("End")
        future.set_result("Done!")

    asyncio.create_task(set_result())
    result = await future  # Waits until result is set
    print(result)

asyncio.run(main())
```
output
```
Start
End
Done!
```