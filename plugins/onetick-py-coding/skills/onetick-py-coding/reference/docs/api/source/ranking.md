# otp.Source.ranking

#### ``Source.ranking(rank_by, show_rank_as, include_tick, bucket_interval=0, bucket_units=None, bucket_time='end', bucket_end_condition=None, boundary_tick_bucket='new', group_by=None, groups_to_display='all', end_condition_per_group=False)``

Ranking **running** aggregation.

Sorts a series of ticks over a bucket interval
using a specified set of tick fields specified in `rank_by`
and adds a new field `RANKING`
with the position of the tick in the sort order
or the percentage of ticks with values less than (or equal) to the value of the tick.

Does not change the order of the ticks.

* **Parameters:**
  * **rank_by** (*str* *or* *list* *or* *dict*) – Set of fields to sort by.
    Can be one field specified by string, list of fields or dictionary with field names
    as keys and `asc` or `desc` string literals as values. Latter allows to specify
    sorting direction. Default direction is `desc`.
  * **show_rank_as** (*str*) – 
    - `order`: calculate number that represents the position of the tick in the sort order
    - `percent_le_values`: calculate the percentage of ticks that have higher or equal value           of the position in the sort order, relative to the tick
    - `percent_lt_values`: calculate the percentage of ticks that have higher value           of the position in the sort order, relative to the tick
    - `percentile_standard`: calculate Percentile Rank of the tick in the sort order.
  * **include_tick** (*bool* *,* *default=False*) – Specifies whether the current tick should be included in calculations
    if `show_rank_as` is `percent_lt_values` or `percentile_standard`.
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
  * **bucket_units** (*Optional* *[**Literal* *[* *'seconds'* *,*  *'ticks'* *,*  *'days'* *,*  *'months'* *,*  *'flexible'* *]* *]* *,* *default=None*) – 

    Set bucket interval units.

    By default, if `bucket_units` and `bucket_end_condition` not specified, set to **seconds**.
    If `bucket_end_condition` specified, then `bucket_units` set to **flexible**.

    If set to **flexible** then `bucket_end_condition` must be set.

    Note that **seconds** bucket unit doesn’t take into account daylight-saving time of the timezone,
    so you may not get expected results when using, for example, 24 \* 60 \* 60 seconds as bucket interval.
    In such case use **days** bucket unit instead.
    See example in ``onetick.py.agg.sum()``.
  * **bucket_time** (*Literal* *[* *'start'* *,*  *'end'* *]* *,* *default=end*) – 

    Control output timestamp.
    * **start**

      the timestamp  assigned to the bucket is the start time of the bucket.
    * **end**

      the timestamp assigned to the bucket is the end time of the bucket.
  * **bucket_end_condition** (*condition* *,* *default=None*) – 

    An expression that is evaluated on every tick. If it evaluates to “True”, then a new bucket is created.
    This parameter is only used if `bucket_units` is set to “flexible”.

    Also can be set via `bucket_interval` parameter by passing ``Operation`` object.
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
  * **end_condition_per_group** (*bool* *,* *default=False*) – 

    Controls application of `bucket_end_condition` in groups.
    * `end_condition_per_group` = True

      `bucket_end_condition` is applied only to the group defined by `group_by`
    * `end_condition_per_group` = False

      `bucket_end_condition` applied across all groups

    This parameter is only used if `bucket_units` is set to “flexible”.

    When set to True, applies to all bucketing conditions. Useful, for example, if you need to specify `group_by`,
    and you want to group items first, and create buckets after that.
* **Return type:**
  `Source`

##### Examples

```
>>> t = otp.Ticks({'A': [1, 2, 3]})
>>> t = t.ranking('A')
>>> otp.run(t)
                     Time  A  RANKING
0 2003-12-01 00:00:00.000  1        3
1 2003-12-01 00:00:00.001  2        2
2 2003-12-01 00:00:00.002  3        1
```

```
>>> t = otp.Ticks({'A': [1, 2, 3]})
>>> t = t.ranking({'A': 'asc'})
>>> otp.run(t)
                     Time  A  RANKING
0 2003-12-01 00:00:00.000  1        1
1 2003-12-01 00:00:00.001  2        2
2 2003-12-01 00:00:00.002  3        3
```

```
>>> t = otp.Ticks({'A': [1, 2, 2, 3, 2, 1]})
>>> otp.run(t.ranking({'A': 'asc'}, show_rank_as='percent_lt_values', include_tick=True))
                     Time  A    RANKING
0 2003-12-01 00:00:00.000  1  66.666667
1 2003-12-01 00:00:00.001  2  16.666667
2 2003-12-01 00:00:00.002  2  16.666667
3 2003-12-01 00:00:00.003  3   0.000000
4 2003-12-01 00:00:00.004  2  16.666667
5 2003-12-01 00:00:00.005  1  66.666667
```

```
>>> otp.run(t.ranking({'A': 'asc'}, show_rank_as='percent_lt_values', include_tick=False))
                     Time  A  RANKING
0 2003-12-01 00:00:00.000  1     80.0
1 2003-12-01 00:00:00.001  2     20.0
2 2003-12-01 00:00:00.002  2     20.0
3 2003-12-01 00:00:00.003  3      0.0
4 2003-12-01 00:00:00.004  2     20.0
5 2003-12-01 00:00:00.005  1     80.0
```

##### SEE ALSO
**RANKING** OneTick event processor
