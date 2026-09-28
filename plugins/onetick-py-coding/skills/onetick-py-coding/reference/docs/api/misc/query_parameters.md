# otp.QueryParameters

### ``class QueryParameters(*, symbol_date=None, concurrency=None, batch_size=None, running=None, query_properties=None)``

Bases: ``object``

OneTick queries have different properties.

They can be set separately for each query in the resulting .otq file.

Some of them have separate setters and can be specified as separate fields here,
others can be specified with `query_properties` dictionary.

Not all properties are specified here, some of them may be set in different places:

> * some options in ``otp.config``
> * some parameters in ``otp.DataSource`` and other source classes
> * some parameters in ``otp.run``
> * etc.

If the query properties are set in different places at the same time,
the value specified last will take precedence.

The default value for all fields here is None, which means that field is not set.

##### Examples

Set the query property when creating source object:

```
>>> t = otp.Tick(A=1, query_parameters=otp.QueryParameters(query_properties={'ALLOW_GRAPH_REUSE': 'TRUE'}))
>>> t['ALLOW_GRAPH_REUSE'] = otp.get_query_property('ALLOW_GRAPH_REUSE')
>>> otp.run(t)
        Time  A ALLOW_GRAPH_REUSE
0 2003-12-01  1              TRUE
```

The property can be overridden when running the query:

```
>>> otp.run(t, query_properties={'ALLOW_GRAPH_REUSE': 'FALSE'})
        Time  A ALLOW_GRAPH_REUSE
0 2003-12-01  1             FALSE
```

* **Parameters:**
  * **symbol_date** (*datetime* *|* *None*)
  * **concurrency** (*int* *|* *None*)
  * **batch_size** (*int* *|* *None*)
  * **running** (*bool* *|* *None*)
  * **query_properties** (*dict* *|* *None*)
