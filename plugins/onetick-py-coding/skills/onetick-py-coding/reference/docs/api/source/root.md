# otp.Source

### *class* Source

Bases: ``object``

Base class for representing Onetick execution graph.
All `onetick-py sources` are derived from this class
and have access to all its methods.

##### Examples

```
>>> data = otp.Tick(A=1)
>>> isinstance(data, otp.Source)
True
```

Also this class can be used to initialize raw source
with the help of `onetick.query` classes, but
it should be done with caution as the user is required to set
such properties as symbol name and tick type manually.

```
>>> data = otp.Source(otq.TickGenerator(bucket_interval=0, fields='long A = 123').tick_type('TT'))
>>> otp.run(data, symbols='LOCAL::')
        Time    A
0 2003-12-04  123
```

* **Parameters:**
  **query_parameters** (*QueryParameters*)
