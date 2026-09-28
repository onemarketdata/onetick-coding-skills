# otp.Source.insert_tick

#### ``Source.insert_tick(fields=None, where=None, preserve_input_ticks=True, num_ticks_to_insert=1, insert_before=True, inplace=False)``

Insert tick.

* **Parameters:**
  * **fields** (dict of str to ``onetick.py.Operation``) – Mapping of field names to some expressions or values.
    These fields in inserted ticks will be set to corresponding values or results of expressions.
    If field is presented in input tick, but not set in `fields` dict,
    then the value of the field will be copied from input tick to inserted tick.
    If parameter `fields` is not set at all, then values for inserted ticks’ fields
    will be default values for fields’ types from input ticks (0 for integers etc.).
  * **where** (``onetick.py.Operation``) – Expression to select ticks near which the new ticks will be inserted.
    By default, all ticks are selected.
  * **preserve_input_ticks** (*bool*) – A switch controlling whether input ticks have to be preserved in output time series or not.
    While the former case results in fields of input ticks to be present in the output time series
    together with those defined by the `fields` parameter,
    the latter case results in only defined fields to be present.
    If a field of the input time series is defined in the `fields` parameter,
    the defined value takes precedence.
  * **num_ticks_to_insert** (*int*) – Number of ticks to insert.
  * **insert_before** (*bool*) – Insert tick before each input tick or after.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Insert tick before each tick with default type values.

```
>>> data = otp.Tick(A=1)
>>> data = data.insert_tick()
>>> otp.run(data)
        Time  A
0 2003-12-01  0
1 2003-12-01  1
```

Insert tick before each tick with field A copied from input tick
and field B set to specified value.

```
>>> data = otp.Tick(A=1)
>>> data = data.insert_tick(fields={'B': 'b'})
>>> otp.run(data)
        Time  B  A
0 2003-12-01  b  1
1 2003-12-01     1
```

Insert two ticks only after first tick.

```
>>> data = otp.Ticks(A=[1, 2, 3])
>>> data = data.insert_tick(where=data['A'] == 1,
...                         insert_before=False,
...                         num_ticks_to_insert=2)
>>> otp.run(data)
                     Time  A
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.000  0
2 2003-12-01 00:00:00.000  0
3 2003-12-01 00:00:00.001  2
4 2003-12-01 00:00:00.002  3
```

##### SEE ALSO
**INSERT_TICK** OneTick event processor
