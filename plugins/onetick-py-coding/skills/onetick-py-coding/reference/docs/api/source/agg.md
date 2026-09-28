# otp.Source.agg

#### ``Source.agg(aggs, running=False, all_fields=False, bucket_interval=0, bucket_time='end', bucket_units=None, bucket_end_condition=None, end_condition_per_group=False, boundary_tick_bucket='new', group_by=None, groups_to_display='all')``

Applies composition of `otp.agg` aggregations

* **Parameters:**
  * **aggs** (*dict* *of* *aggregations*) – 

    aggregation dict:
    * key - output column name for regular aggregations, prefix for column names for tick and multi-column aggregations;
    * value - aggregation
  * **running** (*bool* *,* *default=False*) – 

    See `Aggregation buckets guide` to see examples of how this parameter works.

    Specifies if the aggregation will be calculated as a sliding window.
    `running` and `bucket_interval` parameters determines when new buckets are created.
    * `running` = True

      aggregation will be calculated in a sliding window.
      * `bucket_interval` = N (N > 0)

        Window size will be N. Output tick will be generated when tick “enter” window (**arrival event**) and
        when “exit” window (**exit event**)
      * `bucket_interval` = 0

        Left boundary of window will be set to query start time. For each tick aggregation will be calculated in
        the interval [start_time; tick_t] from query start time to the tick’s timestamp (inclusive).
    * `running` = False (default)

      buckets partition the [query start time, query end time) interval into non-overlapping intervals
      of size `bucket_interval` (with the last interval possibly of a smaller size).
      If `bucket_interval` is set to **0** a single bucket for the entire interval is created.

      Note that in non-running mode OneTick unconditionally divides the whole time interval
      into specified number of buckets.
      It means that you will always get this specified number of ticks in the result,
      even if you have less ticks in the input data.

    Default: False
  * **all_fields** (True, False, ‘when_ticks_exit_window’, ‘first’, ‘last’, ‘high’, ‘low’, ``high_tick()``, ``low_tick()``, default=False) – 

    See `Aggregation buckets guide` to see examples of how this parameter works.
    - If `all_fields` False - output tick will have only aggregation fields.
    - If `all_fields` is True and `running` is False - additional fields will be copied from the first
      tick in the bucket to the output tick.
    - If `all_fields` False and `running` True - output ticks are created when a tick enters or leaves the
      sliding window.
    - If `all_fields` True and `running` True - an output tick is generated only for arrival events,
      but all attributes from the input tick causing an arrival event are copied over to the output tick
      and the aggregation is added as another attribute.
    - If `all_fields` True and `running` “when_ticks_exit_window” -
      an output tick is generated only for exit events,
      but all attributes from the exiting tick are copied over to the output tick
      and the aggregation is added as another attribute.
    - If `all_fields` set to “first”, “last”, “high”, or “low” - explicitly set tick selection policy for all
      fields values. For “high” and “low” “PRICE” field will be selected as an input.
      Otherwise, you will get the runtime error.
      If `all_fields` is set to one of these values, `running` can’t be True.
    - If `all_fields` is aggregation `HighTick` or `LowTick` - set tick selection policy for all fields values to
      “high” or “low” accordingly. But instead of “PRICE” the field selected as input will be set as aggregation’s
      first parameter.
  * **bucket_interval** (int or float or ``Operation`` or ``OnetickParameter`` or ``symbol parameter`` or `datetime offset object`, default=0) – 

    Determines the length of each bucket (units depends on `bucket_units`).

    If ``Operation`` of bool type is passed, acts as `bucket_end_condition`.

    Bucket interval can also be set as a *float* value
    if `bucket_units` is set to *seconds*.
    Note that values less than 0.001 (1 millisecond) are not supported.

    Bucket interval can be set via some of the `datetime offset objects`:
    ``otp.Milli``, ``otp.Second``,
    ``otp.Minute``, ``otp.Hour``,
    ``otp.Day``, ``otp.Month``.
    In this case you could omit setting `bucket_units` parameter.

    Bucket interval can also be set with integer ``OnetickParameter``
    or ``symbol parameter``.
  * **bucket_time** (*Literal* *[* *'start'* *,*  *'end'* *]* *,* *default=end*) – 

    Control output timestamp.
    * **start**

      the timestamp  assigned to the bucket is the start time of the bucket.
    * **end**

      the timestamp assigned to the bucket is the end time of the bucket.
  * **bucket_units** (*Optional* *[**Literal* *[* *'seconds'* *,*  *'ticks'* *,*  *'days'* *,*  *'months'* *,*  *'flexible'* *]* *]* *,* *default=None*) – 

    Set bucket interval units.

    By default, if `bucket_units` and `bucket_end_condition` not specified, set to **seconds**.
    If `bucket_end_condition` specified, then `bucket_units` set to **flexible**.

    If set to **flexible** then `bucket_end_condition` must be set.

    Note that **seconds** bucket unit doesn’t take into account daylight-saving time of the timezone,
    so you may not get expected results when using, for example, 24 \* 60 \* 60 seconds as bucket interval.
    In such case use **days** bucket unit instead.
    See example in ``onetick.py.agg.sum()``.
  * **bucket_end_condition** (*condition* *,* *default=None*) – 

    An expression that is evaluated on every tick. If it evaluates to “True”, then a new bucket is created.
    This parameter is only used if `bucket_units` is set to “flexible”.

    Also can be set via `bucket_interval` parameter by passing ``Operation`` object.
  * **end_condition_per_group** (*bool* *,* *default=False*) – 

    Controls application of `bucket_end_condition` in groups.
    * `end_condition_per_group` = True

      `bucket_end_condition` is applied only to the group defined by `group_by`
    * `end_condition_per_group` = False

      `bucket_end_condition` applied across all groups

    This parameter is only used if `bucket_units` is set to “flexible”.

    When set to True, applies to all bucketing conditions. Useful, for example, if you need to specify `group_by`,
    and you want to group items first, and create buckets after that.
  * **boundary_tick_bucket** (*Literal* *[* *'new'* *,*  *'previous'* *]* *,* *default=new*) – 

    Controls boundary tick ownership.
    * **previous**

      A tick on which `bucket_end_condition` evaluates to “true” belongs to the bucket being closed.
    * **new**

      tick belongs to the new bucket.

    This parameter is only used if `bucket_units` is set to “flexible”
  * **group_by** (*list* *,* *str* *or* *expression* *,* *default=None*) – When specified, each bucket is broken further into additional sub-buckets based on specified field values.
    If ``Operation`` is used then GROUP_{i} column is added. Where i is index in group_by list.
    For example, if Operation is the only element in `group_by` list then GROUP_0 field will be added.
  * **groups_to_display** (*Literal* *[* *'all'* *,*  *'previous'* *]* *,* *default=all*) – Specifies for which sub-buckets (groups) ticks should be shown for each bucket interval.
    By default **all** groups are shown at the end of each bucket interval.
    If this parameter is set to **event_in_last_bucket**, only the groups that received at least one tick
    within a given bucket interval are shown.
* **Return type:**
  ``Source``

##### Examples

By default the whole data is aggregated:

```
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> data = data.agg({'X_SUM': otp.agg.sum('X')})
>>> otp.run(data)
        Time  X_SUM
0 2003-12-04     10
```

Multiple aggregations can be applied at the same time:

```
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> data = data.agg({'X_SUM': otp.agg.sum('X'),
...                  'X_MEAN': otp.agg.average('X')})
>>> otp.run(data)
        Time  X_SUM  X_MEAN
0 2003-12-04     10     2.5
```

Aggregation can be used in running mode:

```
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> data = data.agg({'CUM_SUM': otp.agg.sum('X')}, running=True)
>>> otp.run(data)
                     Time  CUM_SUM
0 2003-12-01 00:00:00.000        1
1 2003-12-01 00:00:00.001        3
2 2003-12-01 00:00:00.002        6
3 2003-12-01 00:00:00.003       10
```

Aggregation can be split in buckets:

```
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> data = data.agg({'X_SUM': otp.agg.sum('X')}, bucket_interval=2, bucket_units='ticks')
>>> otp.run(data)
                     Time  X_SUM
0 2003-12-01 00:00:00.001      3
1 2003-12-01 00:00:00.003      7
```

Running aggregation can be used with buckets too. In this case (all_fields=False and running=True) output ticks
are created when a tick enters or leaves the sliding window (that’s why for this example there are 8 output
ticks for 4 input ticks):

```
>>> data = otp.Ticks(X=[1, 2, 3, 4], offset=[0, 1000, 1500, 3600])
>>> data = data.agg(dict(X_MEAN=otp.agg.average("X"),
...                      X_STD=otp.agg.stddev("X")),
...                 running=True, bucket_interval=2)
>>> otp.run(data)
                     Time  X_MEAN     X_STD
0 2003-12-01 00:00:00.000     1.0  0.000000
1 2003-12-01 00:00:01.000     1.5  0.500000
2 2003-12-01 00:00:01.500     2.0  0.816497
3 2003-12-01 00:00:02.000     2.5  0.500000
4 2003-12-01 00:00:03.000     3.0  0.000000
5 2003-12-01 00:00:03.500     NaN       NaN
6 2003-12-01 00:00:03.600     4.0  0.000000
7 2003-12-01 00:00:05.600     NaN       NaN
```

By default, if you run aggregation with buckets and group_by, then a bucket will be taken first, and after that
grouping and aggregation will be performed:

```
>>> ticks = otp.Ticks(
...     {
...         'QTY': [10, 2, 30, 4, 50],
...         'TRADER': ['A', 'B', 'A', 'B', 'A']
...     }
... )
>>> ticks = ticks.agg(
...     {'SUM_QTY': otp.agg.sum('QTY')}, group_by='TRADER',
...     bucket_interval=3, bucket_units='ticks',
...     running=True, all_fields=True,
... )
>>> otp.run(ticks)
                     Time  TRADER  QTY  SUM_QTY
0 2003-12-01 00:00:00.000       A   10       10
1 2003-12-01 00:00:00.001       B    2        2
2 2003-12-01 00:00:00.002       A   30       40
3 2003-12-01 00:00:00.003       B    4        6
4 2003-12-01 00:00:00.004       A   50       80
```

In the row with index 4, the result of summing up the trades for trader “A” turned out to be 80, instead of 90.
We first took a bucket of 3 ticks, then within it took the group with trader “A” (2 ticks remained) and
added up the volumes.
To prevent this behaviour, and group ticks first, set parameter `end_condition_per_group` to True:

```
>>> ticks = otp.Ticks(
...     {
...         'QTY': [10, 2, 30, 4, 50],
...         'TRADER': ['A', 'B', 'A', 'B', 'A']
...     }
... )
>>> ticks = ticks.agg(
...     {'SUM_QTY': otp.agg.sum('QTY')}, group_by='TRADER',
...     bucket_interval=3, bucket_units='ticks',
...     running=True, all_fields=True,
...     end_condition_per_group=True,
... )
>>> otp.run(ticks)
                     Time  TRADER  QTY  SUM_QTY
0 2003-12-01 00:00:00.000       A   10       10
1 2003-12-01 00:00:00.001       B    2        2
2 2003-12-01 00:00:00.002       A   30       40
3 2003-12-01 00:00:00.003       B    4        6
4 2003-12-01 00:00:00.004       A   50       90
```

Tick aggregations and aggregations, which return more than one output column, could be also used.
Dict key set for an aggregation in `aggs` parameter will be used as prefix
for each output column of this aggregation.

```
>>> data = otp.Ticks(X=[1, 2, 3, 4], Y=[10, 20, 30, 40])
>>> data = data.agg({'X_SUM': otp.agg.sum('X'), 'X_FIRST': otp.agg.first_tick()})
>>> otp.run(data)
        Time  X_FIRST.X  X_FIRST.Y  X_SUM
0 2003-12-04          1         10     10
```

These aggregations can be split in buckets too:

```
>>> data = otp.Ticks(X=[1, 2, 3, 4], Y=[10, 20, 30, 40])
>>> data = data.agg(
...     {'X_SUM': otp.agg.sum('X'), 'X_FIRST': otp.agg.first_tick()},
...     bucket_interval=2, bucket_units='ticks',
... )
>>> otp.run(data)
                     Time  X_FIRST.X  X_FIRST.Y  X_SUM
0 2003-12-01 00:00:00.001          1         10      3
1 2003-12-01 00:00:00.003          3         30      7
```

If all_fields=True an output tick is generated only for arrival events, but all attributes from the input tick
causing an arrival event are copied over to the output tick and the aggregation is added as another attribute:

```
>>> data = otp.Ticks(X=[1, 2, 3, 4], offset=[0, 1000, 1500, 3600])
>>> data = data.agg(dict(X_MEAN=otp.agg.average("X"),
...                      X_STD=otp.agg.stddev("X")),
...                 all_fields=True, running=True)
>>> otp.run(data)
                     Time  X  X_MEAN     X_STD
0 2003-12-01 00:00:00.000  1     1.0  0.000000
1 2003-12-01 00:00:01.000  2     1.5  0.500000
2 2003-12-01 00:00:01.500  3     2.0  0.816497
3 2003-12-01 00:00:03.600  4     2.5  1.118034
```

`all_fields` parameter can be used when there is need to have all original fields in the output:

```
>>> ticks = otp.Ticks(X=[3, 4, 1, 2])
>>> data = ticks.agg(dict(X_MEAN=otp.agg.average("X"),
...                       X_STD=otp.agg.stddev("X")),
...                  all_fields=True)
>>> otp.run(data)
        Time  X  X_MEAN     X_STD
0 2003-12-04  3     2.5  1.118034
```

There are different politics for `all_fields` parameter:

```
>>> data = ticks.agg(dict(X_MEAN=otp.agg.average("X"),
...                       X_STD=otp.agg.stddev("X")),
...                  all_fields="last")
>>> otp.run(data)
        Time  X  X_MEAN     X_STD
0 2003-12-04  2     2.5  1.118034
```

For low/high policies the field selected as input is set this way:

```
>>> data = ticks.agg(dict(X_MEAN=otp.agg.average("X"),
...                       X_STD=otp.agg.stddev("X")),
...                  all_fields=otp.agg.low_tick(data["X"]))
>>> otp.run(data)
        Time  X  X_MEAN     X_STD
0 2003-12-04  1     2.5  1.118034
```

Example of using ‘flexible’ buckets. Here every bucket consists of consecutive upticks.

```
>>> trades = otp.Ticks(PRICE=[194.65, 194.65, 194.65, 194.75, 194.75, 194.51, 194.70, 194.71, 194.75, 194.71])
>>> trades = trades.agg({'COUNT': otp.agg.count(),
...                     'FIRST_TIME': otp.agg.first('Time'),
...                     'LAST_TIME': otp.agg.last('Time')},
...                     bucket_units='flexible',
...                     bucket_end_condition=trades['PRICE'] < trades['PRICE'][-1])
>>> otp.run(trades)
                     Time  COUNT              FIRST_TIME               LAST_TIME
0 2003-12-01 00:00:00.005      5 2003-12-01 00:00:00.000 2003-12-01 00:00:00.004
1 2003-12-01 00:00:00.009      4 2003-12-01 00:00:00.005 2003-12-01 00:00:00.008
2 2003-12-04 00:00:00.000      1 2003-12-01 00:00:00.009 2003-12-01 00:00:00.009
```

##### SEE ALSO
`Aggregations`

**COMPUTE** OneTick event processor

