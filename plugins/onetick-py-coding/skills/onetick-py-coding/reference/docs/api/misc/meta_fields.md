# otp.meta_fields

### *class* MetaFields

Bases: ``object``

OneTick defines several pseudo-columns that can be treated as if they were columns of every tick.

These columns can be accessed directly via ``onetick.py.Source.__getitem__()`` method.

But in case they are used in ``Expr``
they can be accessed via `onetick.py.Source.meta_fields`.

##### Examples

Accessing pseudo-fields as columns or as class properties

```
>>> data = otp.Tick(A=1)
>>> data['X'] = data['_START_TIME']
>>> data['Y'] = otp.Source.meta_fields['_TIMEZONE']
>>> otp.run(data, start=otp.dt(2003, 12, 2), timezone='GMT')
        Time  A          X    Y
0 2003-12-02  1 2003-12-02  GMT
```

#### \_\_getitem_\_(item)

These fields are available:

* `TIMESTAMP` (or `Time`)
* `START_TIME` (or `_START_TIME`)
* `END_TIME` (or `_END_TIME`)
* `TIMEZONE` (or `_TIMEZONE`)
* `DBNAME` (or `_DBNAME`)
* `SYMBOL_NAME` (or `_SYMBOL_NAME`)
* `TICK_TYPE` (or `_TICK_TYPE`)
* `SYMBOL_TIME` (or `_SYMBOL_TIME`)
