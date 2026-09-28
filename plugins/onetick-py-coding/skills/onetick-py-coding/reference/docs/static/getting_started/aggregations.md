# Aggregating

Let’s start with an unaggregated time series.

```python
import onetick.py as otp

s = otp.dt(2024, 2, 1, 9, 30)
e = otp.dt(2024, 2, 1, 9, 30, 1)

q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

Let’s make a note of the total number of trades.

Method ``agg`` can be used to aggregate data.

We can aggregate over the entire queried interval by default:

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
q = q.agg({
    'volume': otp.agg.sum('SIZE'),
    'vwap': otp.agg.vwap('PRICE', 'SIZE'),
    'count': otp.agg.count(),
})
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

Or over fixed buckets (aka bars or windows), for example 100 milliseconds buckets:

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
q = q.agg({
    'volume': otp.agg.sum('SIZE'),
    'vwap': otp.agg.vwap('PRICE', 'SIZE')
}, bucket_interval=.1)
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

Or over a sliding window:

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
q = q.agg({
    'volume': otp.agg.sum('SIZE'),
    'vwap': otp.agg.vwap('PRICE', 'SIZE')
}, bucket_interval=.1, running=True)
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

Note that the number of output ticks is more than the number of  trades. This is due to the output tick being created not only when each input tick enters the window but also when it drops out.

We can display all fields of the incoming tick along with the current values of the sliding window metrics.

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
q = q.agg({
    'volume': otp.agg.sum('SIZE'),
    'vwap': otp.agg.vwap('PRICE', 'SIZE')
}, bucket_interval=.1, running=True, all_fields=True)
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

In this case, we are back to the same number of ticks as the number trades as an output tick is only created on arrival of an input tick.

All of the aggregation operations support grouping.

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
q = q.agg({
    'volume': otp.agg.sum('SIZE'),
    'vwap': otp.agg.vwap('PRICE', 'SIZE')
}, group_by=['EXCHANGE'])
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

Note that in non-running mode OneTick unconditionally divides the whole time interval
into specified number of buckets.
It means that you will always get this specified number of ticks in the result,
even if you have less ticks in the input data.
For example, aggregating this empty data will result in 10 ticks nonetheless:

```python
t = otp.Empty()
t = t.agg({'COUNT': otp.agg.count()}, bucket_interval=0.1)
otp.run(t, start=s, end=e)
```

A list of all aggregations appears `here`. It can also be retrieved with `dir(otp.agg)`.

## Aggregation Use Cases

`Creating Bars`

`Golden Cross strategy`
