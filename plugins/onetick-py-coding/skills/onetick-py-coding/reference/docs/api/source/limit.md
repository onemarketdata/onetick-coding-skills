# otp.Source.limit

#### ``Source.limit(tick_limit, tick_offset=None, apply_across_symbols=None, inplace=False)``

Propagates ticks until the count limit is reached.

Once the limit is reached,
hidden ticks will still continue to propagate until the next regular tick appears.

* **Parameters:**
  * **tick_limit** (*int*) – The number of regular ticks to propagate.
    Must be a non-negative integer or -1, which means no limit.
  * **tick_offset** (*int*) – The number of regular ticks to skip before starting to propagate.
    Must be a non-negative integer.
    By default no ticks are skipped.
  * **apply_across_symbols** (*bool*) – 

    If set to **True**, the tick limit and offset are counted across all symbols combined,
    rather than separately for each individual symbol.
    This parameter applies within symbols of a single database.

    For unbound symbols, ticks are limited for a first symbol,
    then for a next and so on until limit is reached.

    Default: **False**
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing. Otherwise method returns a new modified
    object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Simple example, get first 3 ticks:

```python
data = otp.Ticks(X=[1, 2, 3, 4, 5, 6])
data = data.limit(3)
df = otp.run(data)
print(df)
```

```
                     Time  X
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.001  2
2 2003-12-01 00:00:00.002  3
```

Disable limit by setting it to -1:

```python
data = otp.Ticks(X=[1, 2, 3, 4, 5, 6])
data = data.limit(-1)
df = otp.run(data)
print(df)
```

```
                     Time  X
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.001  2
2 2003-12-01 00:00:00.002  3
3 2003-12-01 00:00:00.003  4
4 2003-12-01 00:00:00.004  5
5 2003-12-01 00:00:00.005  6
```

Setting parameter `tick_offset` can be used to skip first ticks before propagating them.

For example, we can skip first 2 ticks and propagate all other:

```python
data = otp.Ticks(X=[1, 2, 3, 4, 5, 6])
data = data.limit(-1, tick_offset=2)
df = otp.run(data)
print(df)
```

```
                     Time  X
0 2003-12-01 00:00:00.002  3
1 2003-12-01 00:00:00.003  4
2 2003-12-01 00:00:00.004  5
3 2003-12-01 00:00:00.005  6
```

Or we can return ticks from the middle of the stream
by skipping first 2 ticks and then returning next 2 ticks like this:

```python
data = otp.Ticks(X=[1, 2, 3, 4, 5, 6])
data = data.limit(2, tick_offset=2)
df = otp.run(data)
print(df)
```

```
                     Time  X
0 2003-12-01 00:00:00.002  3
1 2003-12-01 00:00:00.003  4
```

Using `apply_across_symbols` to limit ticks through all symbols in one database:

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', date=otp.dt(2024, 2, 1))       
>>> data = data.limit(5)                                                                       
>>> data = data.limit(7, apply_across_symbols=True)                                            
>>> result = otp.run(data, symbols=['AAPL', 'MSFT'])                                           
>>> print(', '.join(f'{len(df)} ticks for symbol {symbol}' for symbol, df in result.items()))  
5 ticks for symbol AAPL, 2 ticks for symbol MSFT
```

##### SEE ALSO
**LIMIT** OneTick event processor
