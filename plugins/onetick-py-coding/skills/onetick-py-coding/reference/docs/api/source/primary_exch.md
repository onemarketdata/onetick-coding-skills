# otp.Source.primary_exch

#### ``Source.primary_exch(discard_on_match=False)``

Propagates the tick if its exchange is the PRIMARY exchange of the security. The primary exchange information
is supplied through the Reference Database. It expects the security level symbol (IBM, not IBM.N) and works
by looking for a field called `EXCHANGE` and filtering out ticks where the field does not match
the primary exchange for the security.

#### NOTE
This EP may not work correctly with OneTick Cloud databases, due to differences
in format of exchange names in RefDB and in tick data.

* **Parameters:**
  * **discard_on_match** (*bool*) – When set to `True` only ticks from non-primary exchange are propagated,
    otherwise ticks from primary exchange are propagated.
  * **self** (*Source*)
* **Return type:**
  Two ``Source`` for each of if-else branches

##### Examples

Get ticks from primary exchange:

```
>>> src = otp.DataSource('SOME_DB', tick_type='TRD', symbols='AAA', date=otp.date(2003, 12, 1))  
>>> src, _ = src.primary_exch()  
>>> otp.run(src, symbol_date=otp.date(2003, 12, 1))  
                     Time  PRICE  SIZE EXCHANGE
0 2003-12-01 00:00:00.001   26.5   150        B
1 2003-12-01 00:00:00.002   25.7    20        B
```

Get all ticks, but mark ticks from primary exchange in column `T`:

```
>>> src = otp.DataSource('SOME_DB', tick_type='TRD', symbols='AAA', date=otp.date(2003, 12, 1))  
>>> primary, other = src.primary_exch()  
>>> primary['T'] = 1  
>>> other['T'] = 0  
>>> data = otp.merge([primary, other])  
>>> otp.run(src, symbol_date=otp.date(2003, 12, 1))  
                     Time  PRICE  SIZE EXCHANGE  T
0 2003-12-01 00:00:00.000   25.2   100        A  0
1 2003-12-01 00:00:00.001   26.5   150        B  1
2 2003-12-01 00:00:00.002   25.7    20        B  1
3 2003-12-01 00:00:00.003   24.8    40        A  0
```

##### SEE ALSO
**PRIMARY_EXCH** OneTick event processor
