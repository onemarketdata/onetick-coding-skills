# otp.oqd.DescriptiveFields

### ``class DescriptiveFields(start=utils.adaptive, end=utils.adaptive, symbol=utils.adaptive)``

Bases: ``Source``

OneQuantData™ source to retrieve a time series of descriptive fields for a symbol.
There will only be ticks on days when some field in the descriptive data changes.
Output ticks will have fields:
OID, END_DATE, COUNTRY, EXCH, NAME,
ISSUE_DESC, ISSUE_CLASS, ISSUE_TYPE, ISSUE_STATUS,
SIC_CODE, IDSYM, TICKER, CALENDAR.

Note: currently actual fields have 9999 year in END_DATE, but it could not fit the
nanosecond timestamp, so it is replaced with 2035-01-01 date.

##### Examples

```
>>> src = otp.oqd.DescriptiveFields()
>>> otp.run(src,
...         symbols='1000001589',
...         start=otp.dt(2020, 3, 1),
...         end=otp.dt(2023, 3, 2),
...         timezone='GMT').iloc[:6]
        Time         OID    END_DATE COUNTRY  EXCH                NAME                   ISSUE_DESC         ISSUE_CLASS ISSUE_TYPE ISSUE_STATUS SIC_CODE    IDSYM TICKER CALENDAR
0 2020-03-01  1000001589  2020-03-23     LUX  EL^X  INVESTEC GLOBAL ST   EUROPEAN HIGH YLD BD INC 2                FUND                  NORMAL           B2PT4G9
1 2020-03-23  1000001589  2020-04-01     LUX  EL^X  NINETY ONE LIMITED   EUROPEAN HIGH YLD BD INC 2                FUND                  NORMAL           B2PT4G9
2 2020-04-01  1000001589  2021-01-01     LUX  EL^X  NINETY ONE LUX S.A   EUROPEAN HIGH YLD BD INC 2                FUND                  NORMAL           B2PT4G9
3 2021-01-01  1000001589  2021-06-18     LUX  EL^X  NINETY ONE LUX S.A   EUROPEAN HIGH YLD BD INC 2                FUND                  NORMAL           B2PT4G9
4 2021-06-18  1000001589  2022-01-01     LUX  EL^X  NINETY ONE LUX S.A  GSF GBL HIGH YLD A2 EUR DIS                FUND                  NORMAL           B2PT4G9
5 2022-01-01  1000001589  2022-01-28     LUX  EL^X  NINETY ONE LUX S.A  GSF GBL HIGH YLD A2 EUR DIS                FUND                  NORMAL           B2PT4G9
```

* **Parameters:**
  * **start** (``datetime.datetime``, ``otp.datetime``, ``onetick.py.adaptive``, default= ``onetick.py.adaptive``) – Start of the interval from which the data should be taken.
    Default is ``onetick.py.adaptive``, making the final query deduce the time
    limits from the rest of the graph.
  * **end** (``datetime.datetime``, ``otp.datetime``, ``onetick.py.adaptive``, default= ``onetick.py.adaptive``) – End of the interval from which the data should be taken.
    Default is ``onetick.py.adaptive``, making the final query deduce the time
    limits from the rest of the graph.
  * **symbol** (str, list of str, `Source`, `query`, ``eval query``, default= ``onetick.py.adaptive``) – Symbol(s) from which data should be taken.
