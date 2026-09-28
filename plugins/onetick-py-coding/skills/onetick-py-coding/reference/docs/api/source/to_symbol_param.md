# otp.Source.to_symbol_param

#### ``Source.to_symbol_param()``

Creates a read-only instance with the same columns except Time.
It is used as a result of a first stage query with symbol params.

##### Examples

```
>>> symbols = otp.Ticks({'SYMBOL_NAME': ['AAPL', 'MSFT'], 'PARAM': ['A', 'B']})
>>> symbol_params = symbols.to_symbol_param()
>>> t = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
>>> t = t[['PRICE']][:2]
>>> t['S_PARAM'] = symbol_params['PARAM']
>>> result = otp.run(t, symbols=symbols, date=otp.dt(2024, 2, 1))
>>> result['AAPL']
                           Time   PRICE S_PARAM
0 2024-02-01 04:00:00.008283417  186.50       A
1 2024-02-01 04:00:00.008290927  185.59       A
```

##### SEE ALSO
`Symbol parameters`
