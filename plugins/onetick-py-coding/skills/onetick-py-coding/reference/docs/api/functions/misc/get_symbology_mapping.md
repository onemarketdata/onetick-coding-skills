# otp.misc.get_symbology_mapping

### ``get_symbology_mapping(dest_symbology, src_symbology=None, symbol=None, timestamp=None)``

Translates and returns the symbol in `dest_symbology` using the current timestamp as the symbol date.

The remaining optional parameters are specified to overwrite
the symbology of the current symbol, the current symbol, and the timestamp, respectively.

* **Parameters:**
  * **dest_symbology** (str, ``Operation``) – Symbology to translate the symbol name to.
  * **src_symbology** (str, ``Operation``) – Symbology from which the symbol will be translated.
    Will be taken from the input symbol name if it has symbology part in it
    or defaults to the symbology of the input database, which is specified in the locator file.
  * **symbol** (str, ``Operation``) – Used to specify the input symbol name.
    By default the symbol name of the query is used.
  * **timestamp** (``Operation``) – They symbol date to use when translating symbol name.
    By default the current timestamp of the tick is used.
* **Return type:**
  ``Operation``

##### Examples

Get the symbol name in OID symbology:

```
>>> data = otp.Tick(A=1, db=None)
>>> data['SYMBOLOGY_MAPPING'] = otp.get_symbology_mapping('OID')
>>> otp.run(data, symbols='TDEQ::US_COMP_SAMPLE::AAPL', date=otp.dt(2024, 2, 1), symbol_date=otp.dt(2024, 2, 1))
        Time  A SYMBOLOGY_MAPPING
0 2024-02-01  1              9706
```

Override source symbology, symbol and symbol date
(Also note that parameters can be set from columns):

```
>>> data = otp.Tick(SYMBOL='MSFT', db=None)
>>> data['SYMBOLOGY_MAPPING'] = otp.get_symbology_mapping('OID', 'TDEQ', data['SYMBOL'], otp.dt(2024, 2, 1))
>>> otp.run(data, symbols='US_COMP_SAMPLE::AAPL', date=otp.dt(2024, 2, 1))
        Time SYMBOL SYMBOLOGY_MAPPING
0 2024-02-01   MSFT            109037
```

##### SEE ALSO
``onetick.py.SymbologyMapping``
