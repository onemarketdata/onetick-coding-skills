# otp.Source.show_symbol_name_in_db

#### ``Source.show_symbol_name_in_db(inplace=False)``

Adds the **SYMBOL_NAME_IN_DB** field to input ticks,
indicating the symbol name of the tick in the database.

* **Parameters:**
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

For example, it can be used to display
the actual symbol name for the contract (e.g., **ESM23**)
instead of the artificial continuous name **ES_r_tdi**.
Notice how actual symbol name can be different for each tick,
e.g. in this case it is different for each month.

```
>>> data = otp.DataSource('TDI_FUT', tick_type='TRD')  
>>> data = data[['PRICE']]  
>>> data = data.first(bucket_interval=31, bucket_units='days')  
>>> data['SYMBOL_NAME'] = data.Symbol.name  
>>> data = data.show_symbol_name_in_db()  
>>> otp.run(data,  
...         symbols='ES_r_tdi', symbol_date=otp.dt(2023, 3, 1),
...         start=otp.dt(2023, 3, 1), end=otp.dt(2023, 5, 1))
                     Time    PRICE SYMBOL_NAME SYMBOL_NAME_IN_DB
0 2023-03-01 00:00:00.549  3976.75    ES_r_tdi             ESH23
1 2023-04-02 18:00:00.039  4127.00    ES_r_tdi             ESM23
```

##### SEE ALSO
**SHOW_SYMBOL_NAME_IN_DB** OneTick event processor
