# otp.Source.pnl_realized

#### ``Source.pnl_realized(computation_method='fifo', output_field_name='PNL_REALIZED', size_field='SIZE', price_field='PRICE', buy_sell_flag_field='BUY_SELL_FLAG', inplace=False)``

Computes the realized Profit and Loss (**PNL**) on each tick and is applicable to both scenarios,
whether selling after buying or buying after selling.

* **Parameters:**
  * **computation_method** (*str*) – 

    This parameter determines the approach used to calculate the realized Profit and Loss (**PnL**).

    Possible options are:
    * `fifo` - Stands for ‘First-In-First-Out,’ is used to calculate Profit and Loss (**PnL**)
      based on the principle that the first trading positions bought are the first ones to be sold,
      or conversely, the first trading positions sold are the first ones to be bought.
  * **output_field_name** (*str*) – 

    This parameter defines the name of the output field.

    Default: **PNL_REALIZED**.
  * **size_field** (str, ``otp.Column``) – The name of the field with size, default is **SIZE**.
  * **price_field** (str, ``otp.Column``) – The name of the field with price, default is **PRICE**.
  * **buy_sell_flag_field** (str, ``otp.Column``) – The name of the field with buy/sell flag, default is **BUY_SELL_FLAG**.
    If the type of this field is string, then possible values are ‘B’ or ‘b’ for buy and ‘S’ or ‘s’ for sell.
    If the type of this field is integer, then possible values are 0 for buy and 1 for sell.
  * **self** (*Source*)
* **Return type:**
  `Source` | None

##### Examples

Let’s generate some data:

```
>>> trades = otp.Ticks(
...     PRICE=[1.0, 2.0, 3.0, 2.5, 4.0, 5.0, 6.0, 7.0, 3.0, 4.0, 1.0],
...     SIZE=[700, 20, 570, 600, 100, 100, 100, 100, 150, 10, 100],
...     SELL_FLAG=[0, 0, 0, 1, 1, 1, 1, 1, 0, 0, 1],
...     SIDE=['B', 'B', 'B', 'S', 'S', 'S', 'S', 'S', 'B', 'B', 'S'],
... )
>>> otp.run(trades)
                      Time  PRICE  SIZE  SELL_FLAG SIDE
0  2003-12-01 00:00:00.000    1.0   700          0    B
1  2003-12-01 00:00:00.001    2.0    20          0    B
2  2003-12-01 00:00:00.002    3.0   570          0    B
3  2003-12-01 00:00:00.003    2.5   600          1    S
4  2003-12-01 00:00:00.004    4.0   100          1    S
5  2003-12-01 00:00:00.005    5.0   100          1    S
6  2003-12-01 00:00:00.006    6.0   100          1    S
7  2003-12-01 00:00:00.007    7.0   100          1    S
8  2003-12-01 00:00:00.008    3.0   150          0    B
9  2003-12-01 00:00:00.009    4.0    10          0    B
10 2003-12-01 00:00:00.010    1.0   100          1    S
```

And then calculate profit and loss metric for it.

First let’s use string `buy_sell_flag_field` field:

```
>>> data = trades.pnl_realized(buy_sell_flag_field='SIDE')  
>>> otp.run(data)[['Time', 'PRICE', 'SIZE', 'SIDE', 'PNL_REALIZED']]  
                      Time  PRICE  SIZE  SIDE  PNL_REALIZED
0  2003-12-01 00:00:00.000    1.0   700     B           0.0
1  2003-12-01 00:00:00.001    2.0    20     B           0.0
2  2003-12-01 00:00:00.002    3.0   570     B           0.0
3  2003-12-01 00:00:00.003    2.5   600     S         900.0
4  2003-12-01 00:00:00.004    4.0   100     S         300.0
5  2003-12-01 00:00:00.005    5.0   100     S         220.0
6  2003-12-01 00:00:00.006    6.0   100     S         300.0
7  2003-12-01 00:00:00.007    7.0   100     S         400.0
8  2003-12-01 00:00:00.008    3.0   150     B           0.0
9  2003-12-01 00:00:00.009    4.0    10     B           0.0
10 2003-12-01 00:00:00.010    1.0   100     S        -200.0
```

We can get the same result using integer `buy_sell_flag_field` field:

```
>>> data = trades.pnl_realized(buy_sell_flag_field='SELL_FLAG')  
>>> otp.run(data)[['Time', 'PRICE', 'SIZE', 'SELL_FLAG', 'PNL_REALIZED']]  
                      Time  PRICE  SIZE  SELL_FLAG  PNL_REALIZED
0  2003-12-01 00:00:00.000    1.0   700          0           0.0
1  2003-12-01 00:00:00.001    2.0    20          0           0.0
2  2003-12-01 00:00:00.002    3.0   570          0           0.0
3  2003-12-01 00:00:00.003    2.5   600          1         900.0
4  2003-12-01 00:00:00.004    4.0   100          1         300.0
5  2003-12-01 00:00:00.005    5.0   100          1         220.0
6  2003-12-01 00:00:00.006    6.0   100          1         300.0
7  2003-12-01 00:00:00.007    7.0   100          1         400.0
8  2003-12-01 00:00:00.008    3.0   150          0           0.0
9  2003-12-01 00:00:00.009    4.0    10          0           0.0
10 2003-12-01 00:00:00.010    1.0   100          1        -200.0
```

##### SEE ALSO
**PNL_REALIZED** OneTick event processor
