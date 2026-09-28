# otp.Source.skip_bad_tick

#### ``Source.skip_bad_tick(field, discard_on_match=False, jump_threshold=2.0, num_neighbor_ticks=5, use_absolute_values=False, inplace=False)``

Discards ticks based on whether the value of the attribute specified by `field` differs from the value
of the same attribute in the surrounding ticks more times than a given threshold.
Uses SKIP_BAD_TICK EP.

* **Parameters:**
  * **field** (str, ``Column``) – Name of the field (must be present in the input tick descriptor).
  * **discard_on_match** (*bool*) – When set to `True` only ticks that did not match the filter are propagated,
    otherwise ticks that satisfy the filter condition are propagated.
  * **jump_threshold** (*float*) – 

    A threshold to determine if a tick is “good” or “bad.”

    Good ticks are the ticks whose `field` value differs less than `jump_threshold` times
    from the `field`’s value of less than or half of the surrounding `num_neighbor_ticks` ticks.
  * **num_neighbor_ticks** (*int*) – The number of ticks before this tick and after this tick to compare a tick against.
  * **use_absolute_values** (*bool*) – When set to `True`, use absolute values of numbers when checking whether they are within the jump threshold.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise, method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Keep ticks whose price did not jump by more than 20% relative to the surrounding ticks:

```
>>> data = otp.Ticks(X=[10, 11, 15, 11, 9, 10])
>>> data = data.skip_bad_tick(field="X", jump_threshold=1.2, num_neighbor_ticks=1)
>>> otp.run(data)
                     Time   X
0 2003-12-01 00:00:00.000  10
1 2003-12-01 00:00:00.001  11
2 2003-12-01 00:00:00.003  11
3 2003-12-01 00:00:00.005  10
```

Same example, but with passing column as `field` parameter:

```
>>> data = otp.Ticks(X=[10, 11, 15, 11, 9, 10])
>>> data = data.skip_bad_tick(field=data["X"], jump_threshold=1.2, num_neighbor_ticks=1)
>>> otp.run(data)
                     Time   X
0 2003-12-01 00:00:00.000  10
1 2003-12-01 00:00:00.001  11
2 2003-12-01 00:00:00.003  11
3 2003-12-01 00:00:00.005  10
```

If you want to keep only “bad ticks”, which don’t match the filter,
set `discard_on_match` parameter to `True`:

```
>>> data = otp.Ticks(X=[10, 11, 15, 11, 9, 10])
>>> data = data.skip_bad_tick(field=data["X"], jump_threshold=1.2, num_neighbor_ticks=1, discard_on_match=True)
>>> otp.run(data)
                     Time   X
0 2003-12-01 00:00:00.002  15
1 2003-12-01 00:00:00.004   9
```

In case, if you need to compare values on an absolute basis, set `use_absolute_values` parameter to `True`:

```
>>> data = otp.Ticks(X=[10, -11, -15, 11, 9, 10])
>>> data = data.skip_bad_tick(field=data["X"], jump_threshold=1.2, num_neighbor_ticks=1, use_absolute_values=True)
>>> otp.run(data)
                     Time   X
0 2003-12-01 00:00:00.000  10
1 2003-12-01 00:00:00.001  -11
2 2003-12-01 00:00:00.003  11
3 2003-12-01 00:00:00.005  10
```

##### SEE ALSO
**SKIP_BAD_TICK** OneTick event processor
