# Order Book Analytics

`onetick-py` offers functions for analyzing tick-by-tick order book. There are three representations of an order book. We’ll show top 3 levels only for the ease of exposition.

A book can be displayed with a tick per level per side. We refer to a level in the book as a ‘price level’ or ‘prl’.

```python
import onetick.py as otp

s = otp.dt(2024, 2, 1, 10)

prl = otp.ObSnapshot(db='CME_SAMPLE', tick_type='PRL_FULL', max_levels=3)
# we can use the same timestamp for the start an the end times when we just need a snapshot
otp.run(prl, symbols=r'NQ\H24', start=s, end=s)
```

Alternatively, a book can show a tick per level with both ask and bid price/size info.

```python
prl = otp.ObSnapshotWide(db='CME_SAMPLE', tick_type='PRL_FULL', max_levels=3)
otp.run(prl, symbols=r'NQ\H24', start=s, end=s)
```

Finally, all levels can be displayed in one tick.

```python
prl = otp.ObSnapshotFlat(db='CME_SAMPLE', tick_type='PRL_FULL', max_levels=3)
print(otp.run(prl, symbols=r'NQ\H24', start=s, end=s))
```

We can output the book (in any of the three representation) on every change to price/size at any of the levels.

```python
prl = otp.ObSnapshotFlat(db='CME_SAMPLE', tick_type='PRL_FULL', max_levels=3, running=True)
prl = prl.drop(r".+TIME\d")
print(otp.run(prl, symbols=r'NQ\H24', start=s, end=s + otp.Milli(100)))
```

The ``otp.ObSnapshot`` method doesn’t require specifying `max_levels`. The entire book is returned when the parameter is not specified.

```python
prl = otp.ObSnapshot(db='CME_SAMPLE', tick_type='PRL_FULL')
otp.run(prl, symbols=r'NQ\H24', start=s, end=s)
```

## Book Imbalance

Let’s find the time weighted book imbalance. The imbalance at a given time is defined as the sum of the bid sizes at the top x levels minus the sum of the ask sizes at the top x levels divided by the sum of these two terms: the values close to 1 mean the book is much heavier on the bid side, close to -1 – on the ask side, equal to zero means the sizes are the same.

We display top 3 levels of the book first on every update at any of these levels. There are three ticks (one per level) to represent the book after each update.

```python
x = 3
prl = otp.ObSnapshotWide(db='CME_SAMPLE', tick_type='PRL_FULL', max_levels=x, running=True)
otp.run(prl, symbols=r'NQ\H24', start=s, end=s + otp.Milli(100))
```

Let’s compute the total ask and bid volumes and the corresponding imbalance.

```python
prl = otp.ObSnapshotWide(db='CME_SAMPLE', tick_type='PRL_FULL', max_levels=x, running=True)
prl = prl.agg({'ask_vol': otp.agg.sum('ASK_SIZE'), 'bid_vol': otp.agg.sum('BID_SIZE')}, bucket_units='ticks', bucket_interval=x)
prl['imb'] = (prl['bid_vol'] - prl['ask_vol']) / (prl['bid_vol'] + prl['ask_vol'])
otp.run(prl, symbols=r'NQ\H24', start=s, end=s + otp.Milli(100))
```

We can also compute that stats for the imbalance over time.

```python
imb_stats = prl.agg({
    'tw_imb': otp.agg.tw_average('imb'),
    'mean':   otp.agg.average('imb'),
    'stdev':  otp.agg.stddev('imb'),
})
otp.run(imb_stats, symbols=r'NQ\H24', start=s, end=s + otp.Milli(100))
```

## Book sweep

There are two versions of book sweep: by price and by quantity. Book sweep by price, takes a price as an input and returns the total quantity available at that price or better. Book sweep by quantity, takes a quantity as an input and returns the VWAP if the quantity were executed immediately.

```python
prl = otp.ObSnapshot(db='CME_SAMPLE', tick_type='PRL_FULL', max_levels=10)
otp.run(prl, symbols=r'NQ\H24', start=s, end=s)
```

```python
def side_to_direction(side):
    return 1 if side == 'ASK' else -1

def sweep_by_price(side, price):
    prl = otp.ObSnapshot(db='CME_SAMPLE', tick_type='PRL_FULL', side=side)
    direction = side_to_direction(side)
    prl = prl.where(direction * prl['PRICE'] <= direction * price)
    prl = prl.agg({'total_qty': otp.agg.sum('SIZE')})
    return otp.run(prl, symbols=r'NQ\H24', start=s, end=s)

print(sweep_by_price('BID', 11896))
print(sweep_by_price('ASK', 11898))
```

```python
def sweep_by_qty(side, qty):
    prl = otp.ObSnapshot(db='CME_SAMPLE', tick_type='PRL_FULL', side=side)
    prl = prl.agg({'total_qty': otp.agg.sum('SIZE')}, running=True, all_fields=True)
    prl = prl.where(prl['total_qty'] - prl['SIZE'] < qty)
    # update the SIZE in the last tick only so that total_qty is exactly qty
    prl['SIZE'] = prl.apply(lambda row: row['SIZE'] - (row['total_qty'] - qty) if row['total_qty'] > qty else row['SIZE'])
    prl = prl.agg({'VWAP': otp.agg.vwap('PRICE', 'SIZE')})
    return otp.run(prl, symbols=r'NQ\H24', start=s, end=s)
print(sweep_by_qty('BID', 10))
print(sweep_by_qty('ASK', 10))
```

## Market By Order

Order Book data may be annotated with ‘key’ field that lets us break down the book by each value of the ‘key’ field. For example, a book could by keyed by market participant ID, allowing us to see the book with the orders of a given market participant only. Some exchanges provide ‘market-by-order’ data where the book is keyed by order id. Set `show_full_detail` to `True` to see the book broken down to the most granular level. The example below is a market-by-order book.

```python
prl = otp.ObSnapshot('CME_SAMPLE', tick_type='PRL_FULL', side='BID', show_full_detail=True)
orders = otp.run(prl, symbols=r'NQ\H24', start=s, end=s)
orders = orders[['ORDER_ID', 'PRICE', 'LEVEL', 'TIME_PRIORITY', 'SIZE', 'BUY_SELL_FLAG', 'ORDER_TYPE']]
orders.head()
```

Market-by-order data can be used to analyze/validate the priority mechanism used by the exchange.

```python
prl = otp.ObSnapshot('CME_SAMPLE', tick_type='PRL_FULL', side='BID', show_full_detail=True)

"""
ORDER_TYPE:
L = Limit order
I = Implied order

Implied liquidity doesn't have priority as it's always last to execute at any price level.
It also doesn't have an order ID, so the IDs that we see in the db are synthetic
(consisting of 1 or 2 for the 1st/2nd implied level, and E/F for the buy/sell side respectively).

In order to rank the orders within a given price point by priority, we need to sort first by ORDER_TYPE (“L” comes before “I”),
then by TIME_PRIORITY (lowest value comes first).
"""
prl = prl.sort(['LEVEL', 'ORDER_TYPE', 'TIME_PRIORITY'], ascending=[True, False, True])
orders = otp.run(prl, symbols=r'NQ\H24', start=s, end=s)
orders = orders[['ORDER_ID', 'PRICE', 'LEVEL', 'TIME_PRIORITY', 'SIZE', 'BUY_SELL_FLAG', 'ORDER_TYPE']]
orders.head()
```
