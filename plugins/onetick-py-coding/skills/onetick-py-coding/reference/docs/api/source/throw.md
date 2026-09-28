# otp.Source.throw

#### ``Source.throw(message='', where=1, scope='query', error_code=1, throw_before_query_execution=False, inplace=False)``

Propagates error or warning or throws an exception (depending on `scope` parameter)
when condition provided by `where` parameter evaluates to True.

Throwing an exception will abort query execution,
propagating error will stop tick propagation and
propagating warning will not change the data processing.

* **Parameters:**
  * **message** (*str* *or* *Operation*) – Message that will be thrown in exception or returned in error message.
  * **where** (*Operation*) – Logical expression that specifies condition when exception or error will be thrown.
  * **scope** ( *'query'* *or*  *'symbol'*) – ‘query’ will throw an exception and ‘symbol’ will propagate an error or warning
    depending on `error_code` parameter.
  * **error_code** (*int*) – 

    When `scope='symbol'`, values from interval `[1, 500]` indicate warnings
    and values from interval `[1500, 2000]` indicate errors.
    Note that tick propagation will not stop when warning is raised,
    and will stop when error is raised.

    This parameter is not used when `scope='query'`.
  * **throw_before_query_execution** (*bool*) – If set to `True`, the exception will be thrown before the execution of the query.
    This option is intended for supplying placeholder queries that must always throw.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

By default, this method will throw an exception on the first tick with empty message:

```
>>> t = otp.Tick(A=1)
>>> t = t.throw()
>>> otp.run(t)  
Traceback (most recent call last):
    ...
Exception: ...: In THROW: ...
    ...
```

You can specify exception message and condition (note that exception now is raised on second tick):

```
>>> t = otp.Ticks(A=[1, 2])
>>> t = t.throw(message='A is ' + t['A'].apply(str), where=(t['A']==2))
>>> otp.run(t)  
Traceback (most recent call last):
    ...
Exception: ...: In THROW: A is 2. ...
    ...
```

Note that exception will not be thrown if there are no ticks:

```
>>> t = otp.Empty()
>>> t = t.throw()
>>> otp.run(t)
Empty DataFrame
Columns: []
Index: []
```

If you need exception to be thrown always, you can use `throw_before_query_execution` parameter:

```
>>> t = otp.Empty()
>>> t = t.throw(throw_before_query_execution=True)
>>> otp.run(t)  
Traceback (most recent call last):
    ...
Exception: ...: In THROW: ...
```

You can throw OneTick errors and warnings instead of exceptions.
Raising error codes from 1 to 500 indicates warnings.
Raising error codes from 1500 to 2000 indicates errors.
Tick propagation stops only when error is raised.

```
>>> t = otp.Ticks(A=[1, 2, 3, 4])
>>> t = t.throw(message='warning A=1', scope='symbol', error_code=2, where=(t['A']==1))
>>> t = t.throw(message='error A=3', scope='symbol', error_code=1502, where=(t['A']==3))
>>> otp.run(t)  
UserWarning: Symbol error: [2] warning A=1
UserWarning: Symbol error: [1502] error A=3
                     Time  A
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.001  2
```

Right now the only supported interface to get errors and warnings
is via `map` output structure and its methods:

```
>>> otp.run(t, symbols='AAPL', output_structure='map').output('AAPL').error
[(2, 'warning A=1'), (1502, 'error A=3')]
```

##### SEE ALSO
**THROW** OneTick event processor
