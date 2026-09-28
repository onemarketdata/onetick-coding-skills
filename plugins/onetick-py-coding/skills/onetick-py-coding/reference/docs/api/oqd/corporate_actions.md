# otp.oqd.CorporateActions

### ``class CorporateActions(start=utils.adaptive, end=utils.adaptive, symbol=utils.adaptive)``

Bases: ``Source``

OneQuantData™ source EP to retrieve a time series of corporate
actions for a symbol.

This source will return all corporate action fields available for a symbol
with EX-Dates between the query start time and end time (end time is not inclusive).  The
timestamp of the series is equal to the EX-Date of the corporate
action with a time of 0:00:00 GMT.

##### Examples

```
>>> src = otp.oqd.CorporateActions()
>>> otp.run(src,
...         symbols='TDEQ::::AAPL',
...         start=otp.dt(2021, 1, 1),
...         end=otp.dt(2021, 8, 6),
...         symbol_date=otp.dt(2021, 2, 18),
...         timezone='GMT')
        Time   OID  ACTION_ID    ACTION_TYPE  ACTION_ADJUST ACTION_CURRENCY  ANN_DATE   EX_DATE  PAY_DATE  REC_DATE           TERM_NOTE TERM_RECORD_TYPE ACTION_STATUS
0 2021-02-05  9706   16799540  CASH_DIVIDEND          0.205             USD  20210127  20210205  20210211  20210208      CASH:0.205@USD                         NORMAL
1 2021-05-07  9706   17098817  CASH_DIVIDEND          0.220             USD  20210428  20210507  20210513  20210510       CASH:0.22@USD                         NORMAL
```

* **Parameters:**
  * **start** (``datetime.datetime``, ``otp.datetime``, ``onetick.py.adaptive``, default= ``onetick.py.adaptive``) – Start of the interval from which the data should be taken.
    Default is ``onetick.py.adaptive``, making the final query deduce the time
    limits from the rest of the graph.
  * **end** (``datetime.datetime``, ``otp.datetime``, ``onetick.py.adaptive``, default= ``onetick.py.adaptive``) – End of the interval from which the data should be taken.
    Default is ``onetick.py.adaptive``, making the final query deduce the time
    limits from the rest of the graph.
  * **symbol** (str, list of str, `Source`, `query`, ``eval query``, default= ``onetick.py.adaptive``) – Symbol(s) from which data should be taken.
