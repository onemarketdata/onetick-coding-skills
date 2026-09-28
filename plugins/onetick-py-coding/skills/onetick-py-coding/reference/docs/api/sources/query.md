# otp.Query

### ``class Query(query_object=None, out_pin=utils.adaptive, symbol=utils.adaptive, start=utils.adaptive, end=utils.adaptive, params=None, schema=None, query_parameters=None, **kwargs)``

Bases: ``Source``

Create data source object from .otq file or query object

* **Parameters:**
  * **query_object** (path or ``query``) – query to use as a data source
  * **out_pin** (*str*) – query output pin name
  * **symbol** (None, ``onetick.py.adaptive``) – Symbol(s) from which data should be taken.
  * **start** (``datetime.datetime``, ``otp.datetime`` or utils.adaptive) – Time interval from which the data should be taken.
  * **end** (``datetime.datetime``, ``otp.datetime`` or utils.adaptive) – Time interval from which the data should be taken.
  * **params** (*dict*) – params to pass to query.
    Only applicable to string `query_object`
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.
