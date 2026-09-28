# otp.Source.corp_actions

#### ``Source.corp_actions(adjustment_date=None, adjustment_date_tz=<class 'onetick.py.utils.types.default'>, fields=None, adjust_rule='PRICE', apply_split=True, apply_spinoff=False, apply_rights=None, apply_cash_dividend=False, apply_stock_dividend=False, apply_security_splice=False, apply_others='', apply_all=False)``

Adjusts values using corporate actions information loaded into OneTick
from the reference data file. To use it, location of reference database must
be specified via OneTick configuration.

* **Parameters:**
  * **adjustment_date** (``onetick.py.date``, ``onetick.py.datetime``, int, str, None, optional) – 

    The date as of which the values are adjusted.
    All corporate actions of the types specified in the parameters
    that lie between the tick timestamp and the adjustment date will be applied to each tick.

    This parameter can be either date or datetime .
    int and str format can be *YYYYMMDD* or *YYYYMMDDhhmmss*.
    When parameter is a date, the time is assumed to be 17:00:00 GMT
    and parameter `adjustment_date_tz` is ignored.

    If it is not set, the values are adjusted as of the end date in the query.

    Notice that the `adjustment date` is not affected neither by  *\_SYMBOL_PARAM._PARAM_END_TIME_NANOS*
    nor by the *apply_times_daily* setting in ``onetick.py.run()``.
  * **adjustment_date_tz** (*str* *,* *optional*) – 

    Timezone for `adjustment date`.

    By default global ``tz`` value is used.
    Local timezone can’t be used so in this case parameter is set to GMT.
    When `adjustment_date` is in YYYYMMDD format, this parameter is set to GMT.
  * **fields** (*str* *,* *optional*) – 

    A comma-separated list of fields to be adjusted. If this parameter is not set,
    some default adjustments will take place if appropriately named fields exist in the tick:
    - If the `adjust_rule` parameter is set to PRICE, and the PRICE field is present,
      it will get adjusted. If the fields ASK_PRICE or BID_PRICE are present, they will get adjusted.
      If fields ASK_VALUE or BID_VALUE are present, they will get adjusted
    - If the `adjust_rule` parameter is set to SIZE, and the SIZE field is present,
      it will get adjusted. If the fields ASK_SIZE or BID_SIZE are present, they will get adjusted.
      If fields ASK_VALUE or BID_VALUE are present, they will get adjusted.
  * **adjust_rule** (*str* *,* *optional*) – 

    When set to PRICE, adjustments are applied under the assumption that fields to be adjusted contain prices
    (adjustment direction is determined appropriately).

    When set to SIZE, adjustments are applied under the assumption that fields contain sizes
    (adjustment direction is opposite to that when the parameter’s value is PRICE).

    By default the value is PRICE.
  * **apply_split** (*bool* *,* *optional*) – If True, adjustments for splits are applied.
  * **apply_spinoff** (*bool* *,* *optional*) – If True, adjustments for spin-offs are applied.
  * **apply_cash_dividend** (*bool* *,* *optional*) – If True, adjustments for cash dividends are applied.
  * **apply_stock_dividend** (*bool* *,* *optional*) – If True, adjustments for stock dividends are applied.
  * **apply_security_splice** (*bool* *,* *optional*) – If True, adjustments for security splices are applied.
  * **apply_others** (*str* *,* *optional*) – A comma-separated list of names of custom adjustment types to apply.
  * **apply_all** (*bool* *,* *optional*) – If True, applies all types of adjustments, both built-in and custom.
  * **apply_rights** (*bool* *|* *None*)
* **Returns:**
  A new source object with applied adjustments.
* **Return type:**
  ``onetick.py.Source``

##### Examples

```
>>> src = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')
>>> src = src[['PRICE']][:5]
>>> src['ORIG_PRICE'] = src['PRICE']
>>> src = src.corp_actions(adjustment_date=otp.date(2024, 2, 8),
...                        fields="PRICE", apply_cash_dividend=True)
>>> otp.run(src, date=otp.date(2024, 2, 9), symbol_date=otp.date(2024, 2, 8))
                           Time   PRICE  ORIG_PRICE
0 2024-02-09 04:00:00.010340517  188.56      188.32
1 2024-02-09 04:00:00.011124015  188.56      188.32
2 2024-02-09 04:00:00.011139241  188.64      188.40
3 2024-02-09 04:00:00.034406735  188.93      188.69
4 2024-02-09 04:00:00.038220459  188.93      188.69
```

##### SEE ALSO
**CORP_ACTIONS** OneTick event processor
