# otp.run_async

### ``async run_async(*args, **kwargs)``

Asynchronous alternative to ``otp.run``.

All parameters are the same.

This function can be used via built-in python `await` syntax
and standard `asyncio` library.

#### NOTE
Internally this function is implemented as ``otp.run`` running in a separate thread.

Threads in python are generally not interruptable,
so some asyncio functionality may not work as expected.

For example, canceling ``otp.run_async`` task by
`timeout`
may block the waiting function
or exiting the python process will block until the task is finished,
depending on python and asyncio back-end implementation.

##### Examples

```
>>> data = otp.Ticks(A=[1, 2, 3])
```

Calling ``otp.run_async`` will create a “coroutine” object:

```
>>> otp.run_async(data) 
<coroutine object run_async at ...>
```

Use `asyncio.run`
to run this coroutine and wait for it to finish:

```
>>> asyncio.run(otp.run_async(data))
                     Time  A
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.001  2
2 2003-12-01 00:00:00.002  3
```

You can use standard `asyncio`
library functions to create and schedule tasks.

In the example below two tasks are executed in parallel,
so total execution time will be around 3 seconds instead of 6:

```
>>> import asyncio
>>> import time
>>> async def parallel_tasks():
...     # pause 1 second on each tick (thus 3 seconds for 3 ticks)
...     task_otp = asyncio.create_task(otp.run_async(data.pause(1000)))
...     # just sleep for 3 seconds
...     task_other = asyncio.create_task(asyncio.sleep(3))
...     otp_result = await task_otp
...     await task_other
...     print(otp_result)
>>> start_time = time.time()
>>> asyncio.run(parallel_tasks())
                     Time  A
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.001  2
2 2003-12-01 00:00:00.002  3
>>> print('Finished in', time.time() - start_time, 'seconds') 
Finished in 3.0108885765075684 seconds
```
