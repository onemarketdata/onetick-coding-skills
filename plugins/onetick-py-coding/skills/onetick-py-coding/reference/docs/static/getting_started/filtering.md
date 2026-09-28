# Filtering

Let’s start with an unfiltered time series:

```python
import onetick.py as otp

s = otp.dt(2024, 2, 1, 9, 30)
e = otp.dt(2024, 2, 1, 9, 30, 1)

q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

We can filter by the value of a field using method ``where``:

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
q = q.where(q['EXCHANGE'] == 'K')
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

Note that all of the filtering is done in OneTick not in Python, which is much more efficient and lets us work with much bigger data sets.

Filtering for a specific trade condition can be done with the string matching methods:

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
q = q.where(q['COND'].str.contains('I'))
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

Trade filter that limits attention to on-exchange continuous trading trades looks like this (it’s used when creating bars):

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
q = q.character_present(q['COND'], 'O6TUHILNRWZ47QMBCGPV', discard_on_match=True)
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

Filters can include ‘and’ and/or ‘or’ clauses:

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
q = q.where((q['EXCHANGE'] == 'K') & (q['PRICE'] > 183.950))
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

We can also return two branches after filtering with method ``__getitem__`` (or ``where_clause``).
The first branch contains all ticks that satisfy the condition and the other branch contains all ticks that do not satisfy the condition:

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
exchange_k, other = q[q['EXCHANGE'] == 'K']
otp.run(exchange_k, start=s, end=e, symbols=['AAPL'])
```

```python
otp.run(other, start=s, end=e, symbols=['AAPL'])
```

We can retrieve ticks by index with the help of
``first`` and ``last``.

The example below returns the second tick:

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
# get the last of the first two ticks, thus getting the second tick
q = q.first(2)
q = q.last()
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

Python-like slice syntax is also supported:

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
# get last three ticks
q = q[-3:]
otp.run(q, start=s, end=e, symbols=['AAPL'])
```
