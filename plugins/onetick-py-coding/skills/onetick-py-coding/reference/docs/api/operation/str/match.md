# otp.Operation.str.match

### ``match(pat, case=True)``

Match the text against a regular expression specified in the `pat` parameter.

* **Parameters:**
  * **pat** (*str* *or* *Column* *or* *Operation*) – A pattern specified via the POSIX extended regular expression syntax.
  * **case** (*bool*) – If `True`, then regular expression is case-sensitive.
* **Returns:**
  `True` if the match was successful, `False` otherwise.
  Note that boolean Operation is converted to float if added as a column.
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks(X=['hello', 'there were 77 ticks'])
>>> data['Y'] = data['X'].str.match(r'\d\d')
>>> otp.run(data)
                     Time                    X    Y
0 2003-12-01 00:00:00.000                hello  0.0
1 2003-12-01 00:00:00.001  there were 77 ticks  1.0
```

Other columns can be used as parameter `pat` too:

```
>>> data = otp.Tick(X='OneTick', PAT='onetick')
>>> data['Y'] = data['X'].str.match(data['PAT'], case=False)
>>> otp.run(data)
        Time        X      PAT    Y
0 2003-12-01  OneTick  onetick  1.0
```

`match` function can also be used as a filter.
For example, to filter on-exchange continuous trading trades:

```
>>> q = otp.DataSource('US_COMP', tick_type='TRD', symbols=['SPY'])  
>>> q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]  
>>> q = q.where(q['COND'].str.match('^[^O6TUHILNRWZ47QMBCGPV]*$'))  
>>> otp.run(q, start=otp.dt(2023, 5, 15, 9, 30), end=otp.dt(2023, 5, 15, 9, 30, 1))  
                            Time    PRICE  SIZE  COND EXCHANGE
0  2023-05-15 09:30:00.000776704  412.220   247              Z
1  2023-05-15 09:30:00.019069440  412.230   100   F          K
..                           ...      ...   ...   ...      ...
```
