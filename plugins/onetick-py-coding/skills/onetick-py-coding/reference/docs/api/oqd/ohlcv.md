# otp.oqd.OHLCV

### ``class OHLCV(exch='all', start=utils.adaptive, end=utils.adaptive, symbol=utils.adaptive)``

Bases: ``Source``

OneQuantData™ source to retrieve a time series of unadjusted
prices for a symbol for one particular pricing exchange of daily OHLCV data.
Output ticks have fields: OPEN, HIGH, LOW, CLOSE, VOLUME, CURRENCY, EXCH.

* **Parameters:**
  * **exch** (*str* *,*  *'all'* *,*  *'main'* *,* *default=all*) – 

    The OneQuantData exchange code for the desired price series. Possible values:
    - ’all’
      : return data for all exchanges;
    - ’main’
      : return data main pricing exchange;
    - any other string value will treated as exchange name to filter data.

    Default: ‘all’.
  * **start** (``datetime.datetime``, ``otp.datetime``, ``onetick.py.adaptive``, default= ``onetick.py.adaptive``) – Start of the interval from which the data should be taken.
    Default is ``onetick.py.adaptive``, making the final query deduce the time
    limits from the rest of the graph.
  * **end** (``datetime.datetime``, ``otp.datetime``, ``onetick.py.adaptive``, default= ``onetick.py.adaptive``) – End of the interval from which the data should be taken.
    Default is ``onetick.py.adaptive``, making the final query deduce the time
    limits from the rest of the graph.
  * **symbol** (str, list of str, `Source`, `query`, ``eval query``, default= ``onetick.py.adaptive``) – Symbol(s) from which data should be taken.

##### Examples

```
>>> src = otp.oqd.OHLCV(exch="USPRIM")
>>> otp.run(src,
...         symbols='BTKR::::GOOGL US',
...         start=otp.dt(2018, 8, 1),
...         end=otp.dt(2018, 8, 2),
...         symbol_date=otp.dt(2018, 8, 1))
                 Time    OID    EXCH CURRENCY     OPEN     HIGH      LOW    CLOSE    VOLUME
0 2018-08-01 00:00:00  74143  USPRIM      USD  1242.73  1245.72  1225.00  1232.99  605680.0
1 2018-08-01 20:00:00  74143  USPRIM      USD  1219.69  1244.25  1218.06  1241.13  596960.0
```
