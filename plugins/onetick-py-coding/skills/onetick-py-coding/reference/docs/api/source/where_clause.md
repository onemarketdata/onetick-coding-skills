# otp.Source.where_clause

#### ``Source.where_clause(condition, discard_on_match=False, stop_on_first_mismatch=False)``

Split source in two branches depending on `condition`:
one branch with ticks that meet the condition and
the other branch with ticks that don’t meet the condition.

Original source object is not modified.

* **Parameters:**
  * **condition** (``Operation``, ``eval()``) – Condition expression to filter ticks or object evaluating another query.
    In the latter case another query should have only one tick as a result with only one field.
  * **discard_on_match** (*bool*) – 

    Inverts the `condition`.

    Ticks that don’t meet the condition will be returned in the first branch,
    and ticks that meet the condition will be returned in the second branch.
  * **stop_on_first_mismatch** (*bool*) – 

    If set, no ticks will be propagated in the first branch
    starting with the first tick that does not meet the `condition`.

    Other branch will contain all ticks starting with the first mismatch, even if they don’t meet the condition.
  * **self** (*Source*)
* **Return type:**
  `tuple``[Source`, `Source`]

##### Examples

Filtering based on expression:

```
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> odd, even = data.where_clause(data['X'] % 2 == 1)
>>> otp.run(odd)
                     Time  X
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.002  3
>>> otp.run(even)
                     Time  X
0 2003-12-01 00:00:00.001  2
1 2003-12-01 00:00:00.003  4
```

Filtering based on the result of another query:

```
>>> another_query = otp.Tick(WHERE='mod(X, 2) = 1')
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> data, _ = data.where_clause(otp.eval(another_query))
>>> otp.run(data)
                     Time  X
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.002  3
```

Using `discard_on_match` parameter to invert the condition:

```
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> even, odd = data.where_clause(data['X'] % 2 == 1, discard_on_match=True)
>>> otp.run(even)
                     Time  X
0 2003-12-01 00:00:00.001  2
1 2003-12-01 00:00:00.003  4
>>> otp.run(odd)
                     Time  X
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.002  3
```

Using `stop_on_first_mismatch` parameter to not propagate ticks after first mismatch:

```
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> data, other = data.where_clause(data['X'] % 2 == 1, stop_on_first_mismatch=True)
>>> otp.run(data)
        Time  X
0 2003-12-01  1
```

But other branch will contain all ticks after the mismatch, even if they don’t meet the condition:

```
>>> otp.run(other)
                     Time  X
0 2003-12-01 00:00:00.001  2
1 2003-12-01 00:00:00.002  3
2 2003-12-01 00:00:00.003  4
```

##### SEE ALSO
``Source.where()``

``Source.__getitem__()``

**WHERE_CLAUSE** OneTick event processor

