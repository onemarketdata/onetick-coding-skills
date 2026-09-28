# otp.Source.virtual_ob

#### ``Source.virtual_ob(quote_source_fields=None, quote_timeout=None, show_full_detail=False, output_book_format='ob', inplace=False)``

Creates a series of fake orders from a time series of best bids and asks. The algorithm used is as follows:
For a tick with the best bid or ask, create an order tick adding the new best bid or ask and an order
withdrawing the old one. Virtual order books can be created for multiple subgroups at once
by using the `quote_source_fields` parameter to specify a list of string fields to be used for grouping.
A separate book will be created for each combination.

* **Parameters:**
  * **quote_source_fields** (*Optional* **[*list* ***Union* *[*[*str* *,* *Column* *]* *]* *]*) – Specifies a list of string fields for grouping quotes.
    The virtual order book is then constructed for each subgroup separately and
    the `SOURCE` field is constructed to contain the description of the group.
  * **quote_timeout** (*Optional* **[*float* *]*) – 

    Specifies the maximum age of a quote that is not stale.
    A quote that is not replaced after more than `quote_timeout` seconds is considered stale,
    and delete orders will be generated for it.

    A value of `quote_timeout` can be fractional (for example, 3.51)
  * **show_full_detail** (*bool*) – 

    If set to `True`, **virtual_ob** will attempt to include
    all fields in the input tick when forming the output tick.

    `ASK_X/BID_X` fields will combine under paired field `X`
    `ASK_X` and `BID_X` must have the same type.

    If only `ASK_X` or `BID_X` exist, output will have `X` and the missing field
    will be assumed to have its default value.

    Paired and non-paired fields must not interfere with each other and the fields originally added by this EP
  * **output_book_format** ( *'prl'* *or*  *'ob'*) – 

    Supported values are `prl` and `ob`. When set to `prl`, field `SIZE` of output ticks represents
    current size for the tick’s source, price, and side, and the EP propagates `PRICE` and `SOURCE`
    as the state keys of its output time series.

    When set to `ob`, field `SIZE` of output ticks represents the delta of size for
    the tick’s source, price, and side, and the state key of the output ticks is empty.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing. Otherwise, method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Basic example

```
>>> data = otp.DataSource(
...     db='US_COMP', symbols='AAPL', tick_type='QTE', date=otp.date(2003, 12, 1)
... )  
>>> data = data[['ASK_PRICE', 'ASK_SIZE', 'BID_PRICE', 'BID_SIZE']]  
>>> data = data.virtual_ob()  
>>> otp.run(data)  
                      Time  PRICE        DELETED_TIME  SIZE  BUY_SELL_FLAG  TICK_STATUS SOURCE
0  2003-12-01 00:00:00.000  22.28 1969-12-31 19:00:00   500              1            0   AAPL
1  2003-12-01 00:00:00.000  21.66 1969-12-31 19:00:00   100              0            0   AAPL
2  2003-12-01 00:00:00.001   21.8 1969-12-31 19:00:00   500              1            0   AAPL
...
```

Specify columns to group quotes

```
>>> data = otp.DataSource(
...     db='US_COMP', symbols='AAPL', tick_type='QTE', date=otp.date(2003, 12, 1)
... )  
>>> data = data[['ASK_PRICE', 'ASK_SIZE', 'BID_PRICE', 'BID_SIZE', 'EXCHANGE']]  
>>> data = data.virtual_ob(['EXCHANGE'])  
>>> otp.run(data)  
                      Time  PRICE        DELETED_TIME  SIZE  BUY_SELL_FLAG  TICK_STATUS SOURCE
0  2003-12-01 00:00:00.000  22.28 1969-12-31 19:00:00   500              1            0      D
1  2003-12-01 00:00:00.000  21.66 1969-12-31 19:00:00   100              0            0      D
2  2003-12-01 00:00:00.001   21.8 1969-12-31 19:00:00   500              1            0      P
...
```

##### SEE ALSO
**VIRTUAL_OB** OneTick event processor
