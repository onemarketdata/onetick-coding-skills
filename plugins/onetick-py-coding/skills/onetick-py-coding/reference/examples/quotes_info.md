# what is Quotes

Quotes represnt ordered by time Best Bid and Offer (BBO, best in perspective of price) ticks in a database.

Usually we store quotes in OneTick database under the 'QTE' tick type.

# what is NBBO

National Bid and Offer (NBBO) ticks we have store under 'NBBO' tick type. Not every database contains the 'NBBO' ticks .
For example, the TAQ_NBBO database contains NBBO ticks for American equities.

# quotes tick schema

Quotes has usually the following schema in OneTick database
- `ASK_PRICE` double - price of an ask
- `ASK_SIZE` long - size of an ask
- `BID_PRICE` double - price of an bid
- `BID_SIZE` long - size of bid

# NBBO tick schema

TODO: 

# calculating spread on quotes

The following code in `onetick.py` shows how the spread can be calculated

```python
import onetick.py as otp

quotes = otp.DataSource(
        db=...,  # use some db that has quotes
        tick_type='QTE',
        symbol='MSFT'
)

# add the SPREAD field for every tick
quotes['SPREAD'] = quotes['ASK_PRICE'] - quotes['BID_PRICE']
```
