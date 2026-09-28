# otp.Source.modify_symbol_name

#### ``Source.modify_symbol_name(symbol_name, inplace=False)``

Modifies the name of the symbol that provides input ticks for this node.
Uses MODIFY_SYMBOL_NAME EP.

* **Parameters:**
  * **symbol_name** (str, ``Column``, ``Operation``) – String or expression with new SYMBOL_NAME value.
    New SYMBOL_NAME must not depend on ticks, if set via expression.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Replacing with static string:

```
>>> data = otp.DataSource('US_COMP_SAMPLE', symbol='AAPL', tick_type='TRD', date=otp.dt(2024, 2, 1))
>>> data = data[['PRICE']][:3]
>>> data = data.modify_symbol_name(symbol_name='MSFT')
>>> otp.run(data)
                           Time   PRICE
0 2024-02-01 04:00:00.016997102  400.15
1 2024-02-01 04:00:00.024299525  402.00
2 2024-02-01 04:00:00.024325756  402.00
```

Replacing with expression:

```
>>> data = otp.DataSource('US_COMP_SAMPLE', symbol='AAPL', tick_type='TRD', date=otp.dt(2024, 2, 1))
>>> data = data[['PRICE']][:3]
>>> data = data.modify_symbol_name(symbol_name=data['_SYMBOL_NAME'].str.replace('P', ''))
>>> otp.run(data)
                           Time  PRICE
0 2024-02-01 04:00:00.097381367  14.33
1 2024-02-01 04:00:00.138908789  14.37
2 2024-02-01 04:00:00.726613365  14.36
```

##### SEE ALSO
**MODIFY_SYMBOL_NAME** OneTick event processor
