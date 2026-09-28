# otp.agg.option_price

### ``option_price(volatility, interest_rate, compute_model, number_of_steps, compute_delta, compute_gamma, compute_theta, compute_vega, compute_rho, volatility_field_name, interest_rate_field_name, option_type_field_name, strike_price_field_name, days_in_year, days_till_expiration_field_name, expiration_date_field_name, all_fields_for_running, running=False, bucket_interval=0, bucket_time='end', bucket_units=None, bucket_end_condition=None, boundary_tick_bucket='new')``

This aggregation requires several parameters to compute the option price.
Those are, OPTION_TYPE, STRIKE_PRICE, EXPIRATION_DATE or DAYS_TILL_EXPIRATION, VOLATILITY, and INTEREST_RATE.
Each parameter can be specified, either via a symbol parameter with the same name or via a tick field,
by specifying the name of that field as an EP parameter, as follows.
Besides, VOLATILITY and INTEREST_RATE can also be specified as parameters. If they are also specified as fields,
the parameters value are ignored.
In either case, the OPTION_TYPE value must be set to either CALL or PUT (case insensitive).
EXPIRATION_DATE is in YYYYMMDD format, a string in case of a symbol parameter and
an integer in case of a tick attribute.
Additionally, NUMBER_OF_STEPS should be specified in case of Cox-Ross-Rubinstein method.

* **Parameters:**
  * **volatility** (*float*) – The historical volatility of the asset’s returns.
  * **interest_rate** (*float*) – The risk-free interest rate.
  * **compute_model** (*str*) – Allowed values are BS and CRR.
    Choose between Black-Scholes (BS) and Cox-Ross-Rubinstein (CRR) models for computing call/put option price.
    Default: BS
  * **number_of_steps** (*int*) – Specifies the number of time steps between the valuation and expiration dates.
    This is a mandatory parameter for CRR model.
  * **compute_delta** (*bool*) – Specifies whether Delta is to be computed or not. This parameter is used only in case of BS model.
    Default: False
  * **compute_gamma** (*bool*) – Specifies whether Gamma is to be computed or not. This parameter is used only in case of BS model.
    Default: False
  * **compute_theta** (*bool*) – Specifies whether Theta is to be computed or not. This parameter is used only in case of BS model.
    Default: False
  * **compute_vega** (*bool*) – Specifies whether Vega is to be computed or not. This parameter is used only in case of BS model.
    Default: False
  * **compute_rho** (*bool*) – Specifies whether Rho is to be computed or not. This parameter is used only in case of BS model.
    Default: False
  * **volatility_field_name** (*str*) – Specifies name of the field, which carries the historical volatility of the asset’s returns.
    Default: empty
  * **interest_rate_field_name** (*str*) – Specifies name of the field, which carries the risk-free interest rate.
    Default: empty
  * **option_type_field_name** (*str*) – Specifies name of the field, which carries the option type (either CALL or PUT).
    Default: empty
  * **strike_price_field_name** (*str*) – Specifies name of the field, which carries the strike price of the option.
    Default: empty
  * **days_in_year** (*int*) – Specifies number of days in a year (say, 365 or 252 (business days, etc.).
    Used with DAYS_TILL_EXPIRATION parameter to compute the fractional years till expiration.
    Default: 365
  * **days_till_expiration_field_name** (*str*) – Specifies name of the field, which carries number of days till expiration of the option.
    Default: empty
  * **expiration_date_field_name** (*str*) – Specifies name of the field, which carries the expiration date of the option, in YYYYMMDD format.
    Default: empty
  * **all_fields_for_running** (*bool*) – Specifies whether all input tick fields should be present in the output ticks when `running` is set to True.
    Default: False.
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
  * **boundary_tick_bucket** (*Literal* *[* *'new'* *,*  *'previous'* *]* *,* *default=new*) – 

    Controls boundary tick ownership.
    * **previous**

      A tick on which `bucket_end_condition` evaluates to “true” belongs to the bucket being closed.
    * **new**

      tick belongs to the new bucket.

    This parameter is only used if `bucket_units` is set to “flexible”

#### NOTE
This aggregation is used with `.apply()`, but latest OneTick builds support also the `.agg()` method.

##### Examples

Black-Scholes with parameters passed through symbol params and calculated delta:

```python
symbol = otp.Tick(SYMBOL_NAME='SYMB')
symbol['OPTION_TYPE'] = 'CALL'
symbol['STRIKE_PRICE'] = 100.0
symbol['DAYS_TILL_EXPIRATION'] = 30
symbol['VOLATILITY'] = 0.25
symbol['INTEREST_RATE'] = 0.05
data = otp.Ticks(PRICE=[100.7, 101.1, 99.5], symbol=symbol)
data = otp.agg.option_price(compute_delta=True).apply(data)
df = otp.run(data)['SYMB']
print(df)
```

```
        Time     VALUE    DELTA
0 2003-12-04  2.800999  0.50927
```

```python
print(data.schema)
```

```
{'VALUE': <class 'float'>, 'DELTA': <class 'float'>}
```

Enable aggregation over running window and keep all fields in the output:

```python
data = otp.Ticks(PRICE=[100.7, 101.1, 99.5], symbol=symbol)
data = otp.agg.option_price(compute_delta=True,
                            running=True,
                            all_fields_for_running=True).apply(data)
df = otp.run(data)['SYMB']
print(df)
```

```
                     Time  PRICE     VALUE     DELTA
0 2003-12-01 00:00:00.000  100.7  3.452071  0.575541
1 2003-12-01 00:00:00.001  101.1  3.686610  0.597086
2 2003-12-01 00:00:00.002   99.5  2.800999  0.509270
```

Cox-Ross-Rubinstein with parameters passed through fields:

```
>>> data = otp.Ticks(
...     PRICE=[100.7, 101.1, 99.5],
...     OPTION_TYPE=['CALL']*3,
...     STRIKE_PRICE=[100.0]*3,
...     DAYS_TILL_EXPIRATION=[30]*3,
...     VOLATILITY=[0.25]*3,
...     INTEREST_RATE=[0.05]*3,
... )
>>> data = otp.agg.option_price(
...     compute_model='CRR',
...     number_of_steps=5,
...     option_type_field_name='OPTION_TYPE',
...     strike_price_field_name='STRIKE_PRICE',
...     days_till_expiration_field_name='DAYS_TILL_EXPIRATION',
...     volatility_field_name='VOLATILITY',
...     interest_rate_field_name='INTEREST_RATE',
... ).apply(data)
>>> otp.run(data)
        Time     VALUE
0 2003-12-04  2.937537
```

Black-Scholes with some parameters passed through parameters:

```
>>> data = otp.Ticks(
...     PRICE=[100.7, 101.1, 99.5],
...     OPTION_TYPE=['CALL']*3,
...     STRIKE_PRICE=[100.0]*3,
...     DAYS_TILL_EXPIRATION=[30]*3,
... )
>>> data = otp.agg.option_price(
...     option_type_field_name='OPTION_TYPE',
...     strike_price_field_name='STRIKE_PRICE',
...     days_till_expiration_field_name='DAYS_TILL_EXPIRATION',
...     volatility=0.25,
...     interest_rate=0.05,
... ).apply(data)
>>> otp.run(data)
        Time     VALUE
0 2003-12-04  2.800999
```

To compute values for each tick in a series, set `bucket_interval=1` and `bucket_units='ticks'`

```
>>> data = otp.Ticks(
...    PRICE=[110.0, 101.0, 112.0],
...    OPTION_TYPE=["CALL"]*3,
...    STRIKE_PRICE=[110.0]*3,
...    DAYS_TILL_EXPIRATION=[30]*3,
...    VOLATILITY=[0.2]*3,
...    INTEREST_RATE=[0.05]*3
... )
>>> data = otp.agg.option_price(
...    option_type_field_name='OPTION_TYPE',
...    strike_price_field_name='STRIKE_PRICE',
...    days_till_expiration_field_name='DAYS_TILL_EXPIRATION',
...    volatility_field_name='VOLATILITY',
...    interest_rate_field_name='INTEREST_RATE',
...    bucket_interval=1,
...    bucket_units='ticks',
... ).apply(data)
>>> otp.run(data)
                     Time     VALUE
0 2003-12-01 00:00:00.000  2.742714
1 2003-12-01 00:00:00.001  0.212927
2 2003-12-01 00:00:00.002  3.945447
```

Usage with the `.agg()` method (on the latest OneTick builds).

```
>>> data = otp.Ticks(
...     PRICE=[100.7, 101.1, 99.5],
...     OPTION_TYPE=['CALL']*3,
...     STRIKE_PRICE=[100.0]*3,
...     DAYS_TILL_EXPIRATION=[30]*3,
... )
>>> data = data.agg({
...     'RESULT': otp.agg.option_price(
...         option_type_field_name='OPTION_TYPE',
...         strike_price_field_name='STRIKE_PRICE',
...         days_till_expiration_field_name='DAYS_TILL_EXPIRATION',
...         volatility=0.25,
...         interest_rate=0.05,
...     )
... })
>>> otp.run(data)
        Time    RESULT
0 2003-12-04  2.800999
```

The following examples show results for different cases of option price calculation.
Results are compared with two online calculators:
`Drexel University` (DU) and
`Wolfram Alpha` (WA).

Call option, strike price 110.0, underlying price 120.0, volatility 20.0%, interest 5.0%, expiring in 15 days.

```
>>> data = {
...     "PRICE": 120.,
...     "OPTION_TYPE": "call",
...     "STRIKE_PRICE": 110.,
...     "DAYS_TILL_EXPIRATION": 15,
...     "VOLATILITY": 0.2,
...     "INTEREST_RATE": 0.05,
... }
>>> data = otp.Tick(**data)
>>> data = otp.agg.option_price(
...     option_type_field_name='OPTION_TYPE',
...     strike_price_field_name='STRIKE_PRICE',
...     days_till_expiration_field_name='DAYS_TILL_EXPIRATION',
...     volatility_field_name='VOLATILITY',
...     interest_rate_field_name='INTEREST_RATE',
...     compute_delta=True,
...     compute_gamma=True,
...     compute_theta=True,
...     compute_vega=True,
...     compute_rho=True
... ).apply(data)
>>> res = otp.run(data).drop("Time", axis=1)
>>> for key, val in res.to_dict(orient='list').items():
...     print(f"{key}={val[0]}")
VALUE=10.248742578738629
DELTA=0.9866897658932824
GAMMA=0.007022082258701294
THETA=-7.430061156928735
VEGA=0.8311067221257422
RHO=4.444686136785831
```

#### Benchmark comparison

| Field   |     OneTick |   DU benchmark |   WA benchmark |
|---------|-------------|----------------|----------------|
| VALUE   | 10.2487     |    10.2487     |         10.249 |
| DELTA   |  0.98669    |     0.98669    |          0.987 |
| GAMMA   |  0.00702208 |     0.00702208 |          0.007 |
| THETA   | -7.43006    |    -7.43006    |         -7.43  |
| VEGA    |  0.831107   |     0.831107   |          0.831 |
| RHO     |  4.44469    |     4.44469    |          4.445 |

Put option, strike price 110.0, underlying price 120.0, volatility 20.0%, interest 5.0%, expiring in 15 days.

```python
data = {
    "PRICE": 120.,
    "OPTION_TYPE": "put",
    "STRIKE_PRICE": 110.,
    "DAYS_TILL_EXPIRATION": 15,
    "VOLATILITY": 0.2,
    "INTEREST_RATE": 0.05,
}
data = otp.Tick(**data)
data = otp.agg.option_price(
    option_type_field_name='OPTION_TYPE',
    strike_price_field_name='STRIKE_PRICE',
    days_till_expiration_field_name='DAYS_TILL_EXPIRATION',
    volatility_field_name='VOLATILITY',
    interest_rate_field_name='INTEREST_RATE',
    compute_delta=True,
    compute_gamma=True,
    compute_theta=True,
    compute_vega=True,
    compute_rho=True
).apply(data)
res = otp.run(data).drop("Time", axis=1)
for key, val in res.to_dict(orient='list').items():
    print(f"{key}={val[0]:.14f}")
```

```
VALUE=0.02294724243400
DELTA=-0.01331023410672
GAMMA=0.00702208225870
THETA=-1.94135092374397
VEGA=0.83110672212574
RHO=-0.06658254802357
```

#### Benchmark comparison

| Field   |     OneTick |   DU benchmark |   WA benchmark |
|---------|-------------|----------------|----------------|
| VALUE   |  0.0229472  |     0.0229472  |          0.023 |
| DELTA   | -0.0133102  |    -0.0133102  |         -0.013 |
| GAMMA   |  0.00702208 |     0.00702208 |          0.007 |
| THETA   | -1.94135    |    -1.94135    |         -1.941 |
| VEGA    |  0.831107   |     0.831107   |          0.831 |
| RHO     | -0.0665825  |    -0.0665825  |         -0.067 |

Put option, strike price 90.0, underlying price 80.0, volatility 30.0%, interest 8.0%, expiring in 20 days.

```python
data = {
    "PRICE": 80.,
    "OPTION_TYPE": "put",
    "STRIKE_PRICE": 90.,
    "DAYS_TILL_EXPIRATION": 20,
    "VOLATILITY": 0.3,
    "INTEREST_RATE": 0.08,
}
data = otp.Tick(**data)
data = otp.agg.option_price(
    option_type_field_name='OPTION_TYPE',
    strike_price_field_name='STRIKE_PRICE',
    days_till_expiration_field_name='DAYS_TILL_EXPIRATION',
    volatility_field_name='VOLATILITY',
    interest_rate_field_name='INTEREST_RATE',
    compute_delta=True,
    compute_gamma=True,
    compute_theta=True,
    compute_vega=True,
    compute_rho=True
).apply(data)
res = otp.run(data).drop("Time", axis=1)
for key, val in res.to_dict(orient='list').items():
    print(f"{key}={val[0]}")
```

```
VALUE=9.739720671039635
DELTA=-0.9429118423759162
GAMMA=0.020391626464263516
THETA=0.9410250231811439
VEGA=2.1453108389800515
RHO=-4.666995510197969
```

#### Benchmark comparison

| Field   |    OneTick |   DU benchmark |   WA benchmark |
|---------|------------|----------------|----------------|
| VALUE   |  9.73972   |      9.73972   |          9.74  |
| DELTA   | -0.942912  |     -0.942912  |         -0.943 |
| GAMMA   |  0.0203916 |      0.0203916 |          0.02  |
| THETA   |  0.941025  |      0.941025  |          0.941 |
| VEGA    |  2.14531   |      2.14531   |          2.145 |
| RHO     | -4.667     |     -4.667     |         -4.667 |

Call option, strike price 90.0, underlying price 80.0, volatility 30.0%, interest 8.0%, expiring in 20 days.

```
>>> data = {
...     "PRICE": 80.,
...     "OPTION_TYPE": "call",
...     "STRIKE_PRICE": 90.,
...     "DAYS_TILL_EXPIRATION": 20,
...     "VOLATILITY": 0.3,
...     "INTEREST_RATE": 0.08,
... }
>>> data = otp.Tick(**data)
>>> data = otp.agg.option_price(
...     option_type_field_name='OPTION_TYPE',
...     strike_price_field_name='STRIKE_PRICE',
...     days_till_expiration_field_name='DAYS_TILL_EXPIRATION',
...     volatility_field_name='VOLATILITY',
...     interest_rate_field_name='INTEREST_RATE',
...     compute_delta=True,
...     compute_gamma=True,
...     compute_theta=True,
...     compute_vega=True,
...     compute_rho=True
... ).apply(data)
>>> res = otp.run(data).drop("Time", axis=1)
>>> for key, val in res.to_dict(orient='list').items():
...     print(f"{key}={val[0]:.13f}")
VALUE=0.1333777785229
DELTA=0.0570881576241
GAMMA=0.0203916264643
THETA=-6.2274824082202
VEGA=2.1453108389801
RHO=0.2429410866523
```

#### Benchmark comparison

| Field   |    OneTick |   DU benchmark |   WA benchmark |
|---------|------------|----------------|----------------|
| VALUE   |  0.133378  |      0.133378  |          0.133 |
| DELTA   |  0.0570882 |      0.0570882 |          0.057 |
| GAMMA   |  0.0203916 |      0.0203916 |          0.02  |
| THETA   | -6.22748   |     -6.22748   |         -6.227 |
| VEGA    |  2.14531   |      2.14531   |          2.145 |
| RHO     |  0.242941  |      0.242941  |          0.243 |

Call option, strike price 140.0, underlying price 150.0, volatility 60.0%, interest 7.0%, expiring in 10 days.

```
>>> data = {
...     "PRICE": 150.,
...     "OPTION_TYPE": "call",
...     "STRIKE_PRICE": 140.,
...     "DAYS_TILL_EXPIRATION": 10,
...     "VOLATILITY": 0.6,
...     "INTEREST_RATE": 0.07,
... }
>>> data = otp.Tick(**data)
>>> data = otp.agg.option_price(
...     option_type_field_name='OPTION_TYPE',
...     strike_price_field_name='STRIKE_PRICE',
...     days_till_expiration_field_name='DAYS_TILL_EXPIRATION',
...     volatility_field_name='VOLATILITY',
...     interest_rate_field_name='INTEREST_RATE',
...     compute_delta=True,
...     compute_gamma=True,
...     compute_theta=True,
...     compute_vega=True,
...     compute_rho=True
... ).apply(data)
>>> res = otp.run(data).drop("Time", axis=1)
>>> for key, val in res.to_dict(orient='list').items():
...     print(f"{key}={val[0]:.13f}")
VALUE=12.2728332229748
DELTA=0.7774682547236
GAMMA=0.0200066930955
THETA=-88.3314253858302
VEGA=7.3997358024512
RHO=2.8588330133030
```

#### Benchmark comparison

| Field   |     OneTick |   DU benchmark |   WA benchmark |
|---------|-------------|----------------|----------------|
| VALUE   |  12.2728    |     12.2728    |         12.27  |
| DELTA   |   0.777468  |      0.777468  |          0.777 |
| GAMMA   |   0.0200067 |      0.0200067 |          0.02  |
| THETA   | -88.3314    |    -88.3314    |        -88.331 |
| VEGA    |   7.39974   |      7.39974   |          7.4   |
| RHO     |   2.85883   |      2.85883   |          2.859 |

Put option, strike price 140.0, underlying price 150.0, volatility 60.0%, interest 7.0%, expiring in 10 days.

```python
data = {
    "PRICE": 150.,
    "OPTION_TYPE": "put",
    "STRIKE_PRICE": 140.,
    "DAYS_TILL_EXPIRATION": 10,
    "VOLATILITY": 0.6,
    "INTEREST_RATE": 0.07,
}
data = otp.Tick(**data)
data = otp.agg.option_price(
    option_type_field_name='OPTION_TYPE',
    strike_price_field_name='STRIKE_PRICE',
    days_till_expiration_field_name='DAYS_TILL_EXPIRATION',
    volatility_field_name='VOLATILITY',
    interest_rate_field_name='INTEREST_RATE',
    compute_delta=True,
    compute_gamma=True,
    compute_theta=True,
    compute_vega=True,
    compute_rho=True).apply(data)
res = otp.run(data).drop("Time", axis=1)
for key, val in res.to_dict(orient='list').items():
    print(f"{key}={val[0]:.13f}")
```

```
VALUE=2.0045973669685
DELTA=-0.2225317452764
GAMMA=0.0200066930955
THETA=-78.5502018957506
VEGA=7.3997358024512
RHO=-0.9694344974913
```

#### Benchmark comparison

| Field   |     OneTick |   DU benchmark |   WA benchmark |
|---------|-------------|----------------|----------------|
| VALUE   |   2.0046    |      2.0046    |          2     |
| DELTA   |  -0.222532  |     -0.222532  |         -0.223 |
| GAMMA   |   0.0200067 |      0.0200067 |          0.02  |
| THETA   | -78.5502    |    -78.5502    |        -78.55  |
| VEGA    |   7.39974   |      7.39974   |          7.4   |
| RHO     |  -0.969434  |     -0.969434  |         -0.969 |

##### SEE ALSO
**OPTION_PRICE** OneTick event processor
