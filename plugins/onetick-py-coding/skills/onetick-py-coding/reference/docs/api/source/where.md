# otp.Source.where

#### ``Source.where(condition, discard_on_match=False, stop_on_first_mismatch=False)``

Filter ticks that meet the `condition`.

Returns new object, original source object is not modified.

* **Parameters:**
  * **condition** (``Operation``, ``eval()``) – Condition expression to filter ticks or object evaluating another query.
    In the latter case another query should have only one tick as a result with only one field.
  * **discard_on_match** (*bool*) – 

    Inverts the `condition`.

    Ticks that don’t meet the condition will be returned.
  * **stop_on_first_mismatch** (*bool*) – If set, no ticks will be propagated starting with the first tick that does not meet the `condition`.
  * **self** (*Source*)
* **Return type:**
  `Source`

##### Examples

Filtering based on expression:

```
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> data = data.where(data['X'] % 2 == 1)
>>> otp.run(data)
                     Time  X
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.002  3
```

Filtering based on the result of another query:

```
>>> another_query = otp.Tick(WHERE='mod(X, 2) = 1')
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> data = data.where(otp.eval(another_query))
>>> otp.run(data)
                     Time  X
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.002  3
```

Using `discard_on_match` parameter to invert the condition:

```
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> data = data.where(data['X'] % 2 == 1, discard_on_match=True)
>>> otp.run(data)
                     Time  X
0 2003-12-01 00:00:00.001  2
1 2003-12-01 00:00:00.003  4
```

Using `stop_on_first_mismatch` parameter to not propagate ticks after first mismatch:

```
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> data = data.where(data['X'] % 2 == 1, stop_on_first_mismatch=True)
>>> otp.run(data)
        Time  X
0 2003-12-01  1
```

##### SEE ALSO
``Source.where_clause()``

``Source.__getitem__()``

**WHERE_CLAUSE** OneTick event processor

