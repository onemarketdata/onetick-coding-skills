# otp.Source.lee_and_ready

#### ``Source.lee_and_ready(qte, quote_delay=0.0, show_quote_fields=False)``

Adds a numeric attribute to each tick in the stream of trade ticks,
the value of which classifies the trade as a buy, a sell, or undefined.

This is an implementation of the Lee and Ready algorithm:
Match up a trade with the most recent good quote that is at least X seconds older than the trade —

* if the trade’s price is closer to the ask price, label trade a buy (1);
* else, if it is closer to the bid price, label it a sell (-1);
* else, if trade’s price is at the mid-quote, then if it is higher than the last trade’s price,
  classify it as a buy (1);
* else, if it is less, classify it as a sell (-1);
* else, if it is the same, classify it the same way as the previous trade was classified.
* If all of these fail, classify the trade as unknown (0).

This method expects two sources as its input: source of trades (`self`) and source of quotes (`qte`).
While ticks propagated by trades source should have the PRICE,SIZE fields,
ticks propagated by `qte` source should have the ASK_PRICE,ASK_SIZE,BID_PRICE,BID_SIZE fields.

Output of this method is a time series of trades ticks
with the Lee and Ready indicator field (**BuySellFlag**) added.

* **Parameters:**
  * **qte** (*Source*) – The source of quotes.
  * **quote_delay** (*float*) – 

    The minimum number of seconds that needs to elapse between the trade and the quote
    before the quote can be considered for a join with the trade.

    The value is a float number.
    Only the first three digits of the fraction are currently used,
    thus the highest supported granularity of quote delay is milliseconds.
    Sub-millisecond parts of the trade’s and the quote’s timestamps are ignored when computing delay between them.
  * **show_quote_fields** (*bool*) – If set to True, the quote fields that classified trade will also be shown for each trade.
    Note that if there were no quotes before trade, then quote fields will be set to 0.
  * **self** (*Source*)
* **Return type:**
  ``Source``

##### Examples

Add field **BuySellFlag** to the `trd` source:

```
>>> import os
>>> trd = otp.CSV(os.path.join(csv_path, 'trd.csv'))
>>> qte = otp.CSV(os.path.join(csv_path, 'qte.csv'))
>>> data = trd.lee_and_ready(qte)
>>> otp.run(data).head(5)
                        Time   PRICE  SIZE  BuySellFlag
0 2003-12-01 09:00:00.086545  178.26   246         -1.0
1 2003-12-01 09:00:00.245208  178.26     1          1.0
2 2003-12-01 09:00:00.245503  178.26     1          1.0
3 2003-12-01 09:00:00.387100  178.21     9          1.0
4 2003-12-01 09:00:00.387105  178.21    12          1.0
```

Fields from `qte` can be added with `show_quote_fields` parameter:

```
>>> data = trd.lee_and_ready(qte, show_quote_fields=True)
>>> data = data.drop(['ASK_SIZE', 'BID_SIZE'])
>>> otp.run(data).head(5)
                        Time   PRICE  SIZE  BuySellFlag               QTE_TIMESTAMP  ASK_PRICE  BID_PRICE
0 2003-12-01 09:00:00.086545  178.26   246         -1.0  2003-12-01 09:00:00.028307     178.80     177.92
1 2003-12-01 09:00:00.245208  178.26     1          1.0  2003-12-01 09:00:00.244626     178.57     177.75
2 2003-12-01 09:00:00.245503  178.26     1          1.0  2003-12-01 09:00:00.244626     178.57     177.75
3 2003-12-01 09:00:00.387100  178.21     9          1.0  2003-12-01 09:00:00.387096     178.57     177.75
4 2003-12-01 09:00:00.387105  178.21    12          1.0  2003-12-01 09:00:00.387096     178.57     177.75
```

Set `quote_delay` parameter to 300 milliseconds:

```
>>> data = trd.lee_and_ready(qte, show_quote_fields=True, quote_delay=0.3)
>>> data = data.drop(['ASK_SIZE', 'BID_SIZE'])
>>> otp.run(data).head(5)
                        Time   PRICE  SIZE  BuySellFlag               QTE_TIMESTAMP  ASK_PRICE  BID_PRICE
0 2003-12-01 09:00:00.086545  178.26   246          0.0  1969-12-31 19:00:00.000000        0.0       0.00
1 2003-12-01 09:00:00.245208  178.26     1          0.0  1969-12-31 19:00:00.000000        0.0       0.00
2 2003-12-01 09:00:00.245503  178.26     1          0.0  1969-12-31 19:00:00.000000        0.0       0.00
3 2003-12-01 09:00:00.387100  178.21     9         -1.0  2003-12-01 09:00:00.087540      180.0     177.62
4 2003-12-01 09:00:00.387105  178.21    12         -1.0  2003-12-01 09:00:00.087540      180.0     177.62
```

##### SEE ALSO
**LEE_AND_READY** OneTick event processor
