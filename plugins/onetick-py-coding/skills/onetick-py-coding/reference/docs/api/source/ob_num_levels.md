# otp.Source.ob_num_levels

#### ``Source.ob_num_levels(running=False, bucket_interval=0, side=None, book_uncross_method=None, dq_events_that_clear_book=None, max_initialization_days=1)``

Returns the number of levels in the order book at the end of each bucket.

* **Parameters:**
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
  * **bucket_interval** (int or `datetime offset object`, default=0) – 

    Determines the length of each bucket in seconds.

    Bucket interval can be set via `datetime offset objects`
    like ``otp.Second``, ``otp.Minute``,
    ``otp.Hour``, ``otp.Day``.
    In this case it will be converted to seconds.
  * **side** (*Literal* *[* *'ASK'* *,*  *'BID'* *]* *,* *default=None*) – Specifies whether the function is to be applied to sell orders (ASK), buy orders (BID), or both (empty).
  * **book_uncross_method** (*Literal* *[* *'REMOVE_OLDER_CROSSED_LEVELS'* *]* *,* *default=None*) – When set to “REMOVE_OLDER_CROSSED_LEVELS”, all ask levels that have price lower or equal to
    the price of a new bid tick get removed from the book, and all bid levels that have price higher or equal
    to the price of a new ask tick get removed from the book.
  * **dq_events_that_clear_book** (*list* **[*str* *]* *,* *default=None*) – A list of names of data quality events arrival of which should clear the order book.
  * **max_initialization_days** (*int* *,* *default=1*) – This parameter specifies how many days back book event processors should go in order to find
    the latest full state of the book.
    The query will not go back resulting number of days if it finds initial book state earlier.
    When book event processors are used after VIRTUAL_OB EP, this parameter should be set to 0.
    When set, this parameter takes precedence over the configuration parameter BOOKS.MAX_INITIALIZATION_DAYS.
* **Return type:**
  `Source`

#### NOTE
This EP supports only seconds as `bucket_interval`.

##### Examples

```
>>> data = otp.DataSource(db='CME_SAMPLE', tick_type='PRL_FULL', symbols=r'NQ\H24')  
>>> data = otp.agg.ob_num_levels(bucket_interval=otp.Second(300)).apply(data)        
>>> otp.run(data, start=otp.dt(2024, 2, 1, 10), end=otp.dt(2024, 2, 1, 11))          
                  Time  ASK_VALUE  BID_VALUE
0  2024-02-01 10:05:00      743.0      830.0
1  2024-02-01 10:10:00      753.0      820.0
2  2024-02-01 10:15:00      741.0      831.0
...
```

##### SEE ALSO
``onetick.py.ObNumLevels()``

**OB_NUM_LEVELS** OneTick event processor

