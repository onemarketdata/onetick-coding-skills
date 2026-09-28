# otp.by_symbol

### ``by_symbol(src, symbol_field, single_invocation=False, db=utils.adaptive_to_default, tick_type=utils.adaptive, start=utils.adaptive, end=utils.adaptive)``

Create a separate data series for each unique value of `symbol_field` in the output of `src`.
`src` must specify enough parameters to be run (e.g., symbols, query range). A typical use case is to split a
single data series (e.g., from a CSV file) into separate data series by symbol. This method is a source.

* **Parameters:**
  * **src** (*Source*) – a query which output is to be split by `symbol_field`
  * **symbol_field** (*str*) – the name of the field carrying symbol name in the `src` query
  * **single_invocation** (*bool* *,* *optional*) – `True` means that the `src` query is run once and the result stored in memory speeding up the execution.
    `False` means that the `src` query is run for every symbol of the query saving memory
    but slowing down query execution.
    Default: `False`
  * **db** (*str* *,* *optional*) – Database for running the query. Doesn’t affect the `src` query. The default value
    is `otp.config['default_db']`.
  * **tick_type** (*str* *,* *optional*) – Tick type for the query. Doesn’t affect the `src` query.
  * **start** (*otp.dt* *,* *optional*) – By default it is taken from the `src` start time
  * **end** (*otp.dt* *,* *optional*) – By default it is taken from the `src` end time
* **Return type:**
  *Source*

##### Examples

```
>>> executions = otp.CSV( 
...     otp.utils.file(os.path.join(cur_dir, 'data', 'example_events.csv')),
...     converters={"time_number": lambda c: c.apply(otp.nsectime)},
...     timestamp_name="time_number",
...     start=otp.dt(2022, 7, 1),
...     end=otp.dt(2022, 7, 2),
...     order_ticks=True
... )[['stock', 'px']]
>>> csv = otp.by_symbol(executions, 'stock') 
>>> trd = otp.DataSource( 
...     db='US_COMP',
...     tick_type='TRD',
...     start=otp.dt(2022, 7, 1),
...     end=otp.dt(2022, 7, 2)
... )[['PRICE', 'SIZE']]
>>> data = otp.join_by_time([csv, trd]) 
>>> result = otp.run(data, symbols=executions.distinct(keys='stock')[['stock']], concurrency=8) 
>>> result['THG'] 
                           Time stock      px   PRICE  SIZE
0 2022-07-01 11:37:56.432947200   THG  148.02  146.48     1
>>> result['TFX'] 
                           Time stock      px   PRICE  SIZE
0 2022-07-01 11:39:45.882808576   TFX  255.61  251.97     1
>>> result['BURL'] 
                           Time stock      px   PRICE  SIZE
0 2022-07-01 11:42:35.125718016  BURL  137.53  135.41     2
```

##### SEE ALSO
**SPLIT_QUERY_OUTPUT_BY_SYMBOL** OneTick event processor
