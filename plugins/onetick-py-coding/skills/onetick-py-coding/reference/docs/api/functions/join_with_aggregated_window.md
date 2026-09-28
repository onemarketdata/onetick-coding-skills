# otp.join_with_aggregated_window

### ``join_with_aggregated_window(agg_src, pass_src, aggregation, boundary_aggr_tick='next', pass_src_delay_msec=0, bucket_interval=0, bucket_units='seconds', output_type_index=None)``

Computes one or more aggregations on `agg_src` time series
and joins the result with each incoming tick from `pass_src` time series.

* **Parameters:**
  * **agg_src** (``onetick.py.Source``) – Input time series to which aggregation will be applied.
  * **pass_src** (``onetick.py.Source``) – Input time series that will be joined with the aggregation result.
  * **aggregation** (*dict*) – Dictionary with aggregation output field names and aggregation objects,
    similar to the one passed to ``onetick.py.Source.agg()`` method.
  * **pass_src_delay_msec** (*int*) – 

    Specifies by how much any incoming tick from the `pass_src` is delayed.

    The effective timestamp of a tick from the `pass_src` with timestamp `T` is `T - pass_src_delay_msec`.
    This parameter may be negative, in which case ticks from `pass_src` will be joined
    with the aggregation result of a later timestamp.
  * **boundary_aggr_tick** (*str*) – 

    Controls the logic of joining ticks with the same timestamp.

    If set to **next**, ticks from `agg_src` with the same timestamp (+ `pass_src_delay_msec`)
    as the latest ticks from `pass_src` will not be included in that tick’s joined aggregation.
  * **bucket_interval** (*int*) – 

    Determines the length of each bucket (units depends on `bucket_units`).

    When this parameter is set to 0 (by default),
    the computation of the aggregation is performed for all ticks starting from the query’s start time
    and until `pass_src` effective tick timestamp `T - pass_src_delay_timestamp`,
    regardless of the value of `bucket_units`.
  * **bucket_units** ( *'seconds'* *,*  *'ticks'* *,*  *'days'* *,*  *'months'*) – Set bucket interval units.
  * **output_type_index** (*int*) – Specifies index of source between `agg_src` and `pass_src`
    from which type and properties of output object will be taken.
    Useful when merging sources that inherited from ``Source``.
    By default, output object type will be ``Source``.
* **Return type:**
  ``onetick.py.Source``

##### Examples

```
>>> agg_src = otp.Ticks(A=[0, 1, 2, 3, 4, 5, 6])
>>> pass_src = otp.Ticks(B=[1, 3, 5], offset=[1, 3, 5])
>>> otp.run(agg_src)
                     Time  A
0 2003-12-01 00:00:00.000  0
1 2003-12-01 00:00:00.001  1
2 2003-12-01 00:00:00.002  2
3 2003-12-01 00:00:00.003  3
4 2003-12-01 00:00:00.004  4
5 2003-12-01 00:00:00.005  5
6 2003-12-01 00:00:00.006  6
```

```
>>> otp.run(pass_src)
                     Time  B
0 2003-12-01 00:00:00.001  1
1 2003-12-01 00:00:00.003  3
2 2003-12-01 00:00:00.005  5
```

By default the aggregation is applied to the ticks from `agg_src` in the bucket
from query start time until (but not including) the *effective* timestamp of the tick from `pass_src`:

```python
agg_src = otp.Ticks(A=[0, 1, 2, 3, 4, 5, 6])
pass_src = otp.Ticks(B=[1, 3, 5], offset=[1, 3, 5])
data = otp.join_with_aggregated_window(
    agg_src, pass_src, {
        'SUM': otp.agg.sum('A'),
        'COUNT': otp.agg.count(),
    }
)
df = otp.run(data)
print(df)
```

```
                     Time  SUM  COUNT  B
0 2003-12-01 00:00:00.001    0      1  1
1 2003-12-01 00:00:00.003    3      3  3
2 2003-12-01 00:00:00.005   10      5  5
```

If you want ticks from `agg_src` with timestamp equal to *effective* timestamp of tick from `pass_src`
to be included in bucket, you can set `boundary_aggr_tick` to `previous`:

```python
agg_src = otp.Ticks(A=[0, 1, 2, 3, 4, 5, 6])
pass_src = otp.Ticks(B=[1, 3, 5], offset=[1, 3, 5])
data = otp.join_with_aggregated_window(
    agg_src, pass_src, {
        'SUM': otp.agg.sum('A'),
        'COUNT': otp.agg.count(),
    },
    boundary_aggr_tick='previous',
)
df = otp.run(data)
print(df)
```

```
                     Time  SUM  COUNT  B
0 2003-12-01 00:00:00.001    1      2  1
1 2003-12-01 00:00:00.003    6      4  3
2 2003-12-01 00:00:00.005   15      6  5
```

Set parameters `bucket_interval` and `bucket_units` to control the size of the aggregation bucket.
For example, to aggregate buckets of two ticks:

```python
agg_src = otp.Ticks(A=[0, 1, 2, 3, 4, 5, 6])
pass_src = otp.Ticks(B=[1, 3, 5], offset=[1, 3, 5])
data = otp.join_with_aggregated_window(
    agg_src, pass_src, {
        'SUM': otp.agg.sum('A'),
        'COUNT': otp.agg.count(),
    },
    boundary_aggr_tick='previous',
    bucket_interval=2,
    bucket_units='ticks',
)
df = otp.run(data)
print(df)
```

```
                     Time  SUM  COUNT  B
0 2003-12-01 00:00:00.001    1      2  1
1 2003-12-01 00:00:00.003    5      2  3
2 2003-12-01 00:00:00.005    9      2  5
```

By default the *effective* timestamp of the tick from `pass_src` is the same as original.
It can be changed with parameter `pass_src_delay_msec`.
The *effective* timestamp of the tick is calculated with `T - pass_src_delay_msec`,
and parameter `pass_src_delay_msec` can be negative too.
This allows to shift bucket end boundary like this:

```python
agg_src = otp.Ticks(A=[0, 1, 2, 3, 4, 5, 6])
pass_src = otp.Ticks(B=[1, 3, 5], offset=[1, 3, 5])
data = otp.join_with_aggregated_window(
    agg_src, pass_src, {
        'SUM': otp.agg.sum('A'),
        'COUNT': otp.agg.count(),
    },
    boundary_aggr_tick='previous',
    pass_src_delay_msec=-1,
)
df = otp.run(data)
print(df)
```

```
                     Time  SUM  COUNT  B
0 2003-12-01 00:00:00.001    3      3  1
1 2003-12-01 00:00:00.003   10      5  3
2 2003-12-01 00:00:00.005   21      7  5
```

Use parameter `output_type_index` to specify which input class to use to create output object.
It may be useful in case some custom user class was used as input:

```python
class CustomTick(otp.Tick):
    def custom_method(self):
        return 'custom_result'
data1 = otp.Tick(A=1)
data2 = CustomTick(B=2)
data = otp.join_with_aggregated_window(
    data1, data2, {'A': otp.agg.count()},
    boundary_aggr_tick='previous',
    output_type_index=1,
)
print(type(data))
print(repr(data.custom_method()))
print(otp.run(data))
```

```
<class 'onetick.py.functions.CustomTick'>
'custom_result'
        Time  A  B
0 2003-12-01  1  2
```

Use-case: check the volume in the 60 seconds following this trade (not including this trade):

```
>>> data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', symbols='MSFT', date=otp.dt(2024, 2, 1))
>>> data = data[['PRICE', 'SIZE']][:6]
>>> otp.run(data)
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.016997102  400.15    42
1 2024-02-01 04:00:00.024299525  402.00    30
2 2024-02-01 04:00:00.024325756  402.00    10
3 2024-02-01 04:00:00.024358407  400.00    10
4 2024-02-01 04:00:00.024362235  399.99     5
5 2024-02-01 04:00:00.024393291  399.99    15
```

```python
data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', symbols='MSFT', date=otp.dt(2024, 2, 1))
data = otp.join_with_aggregated_window(
    data, data, {'VOLUME': otp.agg.sum('SIZE')},
    boundary_aggr_tick='next',
    pass_src_delay_msec=-60000,
    bucket_interval=60, bucket_units='seconds',
)
data = data[['VOLUME', 'PRICE', 'SIZE']][:10]
df = otp.run(data)
print(df)
```

```
                           Time  VOLUME   PRICE  SIZE
0 2024-02-01 04:00:00.016997102    2031  400.15    42
1 2024-02-01 04:00:00.024299525    1989  402.00    30
2 2024-02-01 04:00:00.024325756    1989  402.00    10
3 2024-02-01 04:00:00.024358407    1989  400.00    10
4 2024-02-01 04:00:00.024362235    1989  399.99     5
5 2024-02-01 04:00:00.024393291    1989  399.99    15
6 2024-02-01 04:00:00.024396937    1989  399.81    13
7 2024-02-01 04:00:00.024449244    1989  399.81    50
8 2024-02-01 04:00:00.024545103    1989  399.81    10
9 2024-02-01 04:00:00.026232445    1846  400.00     4
```

##### SEE ALSO
**JOIN_WITH_AGGREGATED_WINDOW** OneTick event processor
