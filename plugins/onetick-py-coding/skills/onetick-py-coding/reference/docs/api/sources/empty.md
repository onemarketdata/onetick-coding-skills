# otp.Empty

### ``class Empty(db=utils.adaptive_to_default, symbol=utils.adaptive_to_default, tick_type=utils.adaptive, start=utils.adaptive, end=utils.adaptive, schema=None, query_parameters=None, **kwargs)``

Bases: ``Source``

Empty data source

* **Parameters:**
  * **db** (*str*) – Name of the database from which to take schema.
  * **symbol** (str, list of str, ``Source``, ``query``, ``eval query``) – Symbol(s) from which data should be taken.
  * **tick_type** (*str* *,*) – Name of the tick_type from which to take schema.
  * **start** (``datetime.datetime``, ``otp.datetime``,                     ``onetick.py.adaptive``) – Time interval from which the data should be taken.
  * **end** (``datetime.datetime``, ``otp.datetime``,                     ``onetick.py.adaptive``) – Time interval from which the data should be taken.
  * **schema** (*dict*) – Schema to use in case db and/or tick_type are not set.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.
  * **kwargs** – Deprecated. Use `schema` instead.
    Schema to use in case db and/or tick_type are not set.

##### Examples

Empty source returns no data:

```
>>> data = otp.Empty()
>>> otp.run(data)
Empty DataFrame
Columns: []
Index: []
```

But we can still define schema if needed:

```
>>> data = otp.Empty(schema={'A': str, 'B': int})
>>> otp.run(data)
Empty DataFrame
Columns: [A, B, Time]
Index: []
>>> data.schema
{'A': <class 'str'>, 'B': <class 'int'>}
```
