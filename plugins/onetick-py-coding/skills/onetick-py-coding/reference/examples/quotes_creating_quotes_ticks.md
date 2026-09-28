# creating quotes ticks with otp.Ticks

This example creates two sequential trade ticks with timestamps +3 ms from the query start for the first tick and +750 ms from the query start. The first quote has ask price 103.0 when the second has 103.0. The have the same ask quantity. The bid prices 99.0 and 100.0 accordingly. The bid quantity is also the same.

```python
import onetick.py as otp

quotes = otp.Ticks({
        "offset": [3, 750],
        "ASK_PRICE": [102.0, 103.0],
        "BID_PRICE": [99.0, 100.0],
        "ASK_SIZE": [30] * 2,
        "BID_SIZE": [25] * 2,
})
```
