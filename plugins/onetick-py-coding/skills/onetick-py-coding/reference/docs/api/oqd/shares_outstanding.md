# otp.oqd.SharesOutstanding

### ``class SharesOutstanding(start=otp.utils.adaptive, end=otp.utils.adaptive, symbol=otp.utils.adaptive)``

Bases: ``Source``

Logic is implemented in OQD_SOURCE_SHO EP to retrieve a time series of shares
outstanding for a stock.

The source retrieves a time series of shares outstanding
for a stock. This source only applies to stocks or securities that have
published shares outstanding data.

The series represents total shares outstanding and is not free float
adjusted.

Note: currently actual fields have 9999 year in END_DATE, but it could not fit the
nanosecond timestamp, so it is replaced with 2035-01-01 date.

##### Examples

```
>>> src = otp.oqd.SharesOutstanding()
>>> otp.run(src,
...         symbols='TDEQ::::AAPL',
...         start=otp.dt(2021, 1, 1),
...         end=otp.dt(2021, 8, 6),
...         symbol_date=otp.dt(2021, 2, 18),
...         timezone='GMT')
        Time   OID   END_DATE REPORT_MONTH        SHARES
0 2021-01-01  9706 2021-01-06       202009  1.700180e+10
1 2021-01-06  9706 2021-01-29       202009  1.682326e+10
2 2021-01-29  9706 2021-05-03       202012  1.678810e+10
3 2021-05-03  9706 2021-07-30       202103  1.668763e+10
4 2021-07-30  9706 2021-10-29       202106  1.653017e+10
```

* **Parameters:**
  * **start** (``datetime.datetime``, ``otp.datetime``, ``onetick.py.adaptive``, default= ``onetick.py.adaptive``) – Start of the interval from which the data should be taken.
    Default is ``onetick.py.adaptive``, making the final query deduce the time
    limits from the rest of the graph.
  * **end** (``datetime.datetime``, ``otp.datetime``, ``onetick.py.adaptive``, default= ``onetick.py.adaptive``) – End of the interval from which the data should be taken.
    Default is ``onetick.py.adaptive``, making the final query deduce the time
    limits from the rest of the graph.
  * **symbol** (str, list of str, `Source`, `query`, ``eval query``, default= ``onetick.py.adaptive``) – Symbol(s) from which data should be taken.
