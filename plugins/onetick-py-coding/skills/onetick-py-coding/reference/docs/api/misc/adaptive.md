# otp.adaptive

### *class* adaptive

Bases: ``object``

This class is mostly used as the default value for the functions’ parameters
when the value of `None` has some other meaning
or when the meaning of the parameter depends on the other parameter’s values,
`otp.config` options or the context.

##### Examples

For example, setting ``DataSource`` `symbols` parameter
to `otp.adaptive` allows to set symbols when running the query later.

```
>>> data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', symbols=otp.adaptive)
>>> data = data[['PRICE']][:3]
>>> otp.run(data, date=otp.dt(2024, 2, 1), symbols='AAPL')
                           Time   PRICE
0 2024-02-01 04:00:00.008283417  186.50
1 2024-02-01 04:00:00.008290927  185.59
2 2024-02-01 04:00:00.008291153  185.49
```

This is the default value of `symbols` parameter, so omitting it also works:

```
>>> data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
>>> data = data[['PRICE']][:3]
>>> otp.run(data, date=otp.dt(2024, 2, 1), symbols='AAPL')
                           Time   PRICE
0 2024-02-01 04:00:00.008283417  186.50
1 2024-02-01 04:00:00.008290927  185.59
2 2024-02-01 04:00:00.008291153  185.49
```
