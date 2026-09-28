# Symbologies

We can query data using different symbologies.

In this case parameter `symbol_date` of ``otp.run`` function should be specified.
This parameter specifies the date when queried securities had specified names
and it is used to determine the names that the security has between the query start and end times.

```python
import onetick.py as otp

s = otp.dt(2024, 2, 1, 9, 30)
e = otp.dt(2024, 2, 1, 9, 30, 1)

trd = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
trd = trd[['PRICE', 'SIZE', 'EXCHANGE', 'COND']]

otp.run(trd, start=s, end=e, symbol_date=otp.dt(2024, 2, 1), symbols=['AAPL'])
```

```python
otp.run(trd, start=s, end=e,
        symbol_date=otp.dt(2024, 2, 1), symbols=['BSYM::::AAPL US Equity'])
```

Examples of supported symbologies include:

- `BSYM::AAPL US Equity` (Bloomberg Symbol)
- `FGC::BBG000B9XRY4`    (Composite FIGI)
- `CUS::037833100`       (CUSIP)
- `ISN::GB00BH4HKS39`    (ISIN)
- `SED::BH4HKS3`         (SEDOL)

We can create a mapping between symbologies.

```python
figi = otp.Symbols('US_COMP_SAMPLE', symbology='FGV', show_original_symbols=True, for_tick_type='TRD')
otp.run(figi, start=s, end=e)
```

```python
btkr = otp.Symbols('US_COMP_SAMPLE', symbology='BTKR', show_original_symbols=True, for_tick_type='TRD')
otp.run(btkr, start=s, end=e)
```

```python
figi = otp.Symbols('US_COMP_SAMPLE', symbology='FGV', show_original_symbols=True, for_tick_type='TRD')
btkr = otp.Symbols('US_COMP_SAMPLE', symbology='BTKR', show_original_symbols=True, for_tick_type='TRD')

mapping = otp.functions.join(figi, btkr, on=btkr['ORIGINAL_SYMBOL_NAME']==figi['ORIGINAL_SYMBOL_NAME'], how="inner")

mapping  = mapping.rename({'RIGHT_SYMBOL_NAME'   : 'BTKR',
                           'SYMBOL_NAME'         : 'FIGI',
                           'ORIGINAL_SYMBOL_NAME': 'DB_SYMBOL'})

mapping = mapping[['FIGI', 'BTKR', 'DB_SYMBOL']]

otp.run(mapping, start=s, end=e)
```

# Symbol name changes

Symbol name changes are easy to handle with the `symbol_date` parameter
of ``otp.run`` which provides the “as of” date for the symbols.
For example, we can pass `META` while setting `symbol_date` to any date after 2022-06-09 or `FB` and `symbol_date` before 2022-06-09.

```python
trd = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
otp.run(trd, start=s, end=e, symbols='META', symbol_date=otp.dt(2023, 2, 2))
```

```python
trd = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
otp.run(trd, start=s, end=e, symbols='FB', symbol_date=otp.dt(2021, 2, 2))
```
