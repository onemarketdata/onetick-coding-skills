# The cross symbols logic 

There is a special technique when logic should be executed for all symbols or for some symbols and then result merged together to compare or select from ticks across all symbols.
We usually name this technique as the cross symbol.

The following code illustrates the general schema of such technique

```python

def per_symbol_logic(symbol):
    # logic that should be exectued implemented invividually per symbol
    # the `symbol` paramter allows to access symbol paramters and understand the symbol name

    # it fetches data from the 'SOME_DB' database with the 'SOME_TICK_TYPE' tick type
    # the `symbol` will be used as a ticker to get data here 
    data = otp.DataSource(db='SOME_DB', tick_type='SOME_TICK_TYPE')

    # shows how to get the current ticker / symbol name
    data['TICKER_NAME'] = symbol.Name

    # some logic
    ...

    return data


# the `merged_data` contains merged ticks cross all
# symbols are mentioned in the `symbols` parameter
merged_data = otp.merge(
    [per_logic_sym],
    symbols=otp.Symbols(db='SOME_DB')  # specifies what symbols to use
) 

# some logic for all ticks
...
```

Also, from the symantic point of view the `per_symbol_logic` can be considered as the pre-processing function and logic after the `otp.merge` as post-processing.


# Get most traded by volume symbols / ticks in a given datbabase. Example of the cross symbols logic

The following code illustrates how to get 5 symbols with largest traded volume in US_COMP database on 2023/12/6

```python
import onetick.py as otp

# calculate volume per symbol
trades = otp.DataSource(db='US_COMP', tick_type='TRD')
trades = trades.agg({'VOLUME': otp.agg.sum('SIZE')})

# save ticker name
trades['TICKER'] = trades.Symbol.name

# merge results for all symbols together
cross_symbol = otp.merge(trades, symbols=otp.Symbols(db='US_COMP'))

# sort and select last 5 with largest volume
cross_symbol = cross_symbol.sort('VOLUME')
top_symbols = cross_symbol[-5:]

df = otp.run(top_symbols, date=otp.dt(2023, 12, 6))
```
