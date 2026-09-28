# otp.query

### ``class query(path, *config, **params)``

Bases: ``object``

Constructs a query object with a certain path.
Keyword arguments specify query parameters.

You also can pass an instance of `otp.query.config` class as the second positional argument to
specify a query.

* **Parameters:**
  * **path** (*str*) – 

    path to an .otq file.
    If path is relative, then it’s assumed that file is located in one of the directories
    specified in OneTick `OTQ_FILE_PATH` configuration variable.
    If there are more than one query in the file, then its name should be specified
    in the format `<path>::<query-name>`.

    Also prefix `remote://<database-name>::` can be used to specify if query is located
    on the remote server.
    If such path exists locally too, then this file is inspected to get info about query and its pins
    as we can’t get this info from remote server.
  * **config** – optional `otp.query.config` object.
    This object can be used to specify different query options, e.g. output columns.
  * **params** – parameters for the query.
    Dictionary if parameters’ names and their values.

##### Examples

Adding local query and applying it to the source:

```
>>> q = otp.query('/otqs/some.otq::some_query', PARAM1='val1', PARAM2=3.14)  
>>> t = otp.Tick(A=1)
>>> t = t.apply(q)  
```

Adding remote query:

```
>>> otp.query('remote://DATABASE::/otqs/some.otq::some_query', PARAM1='val1', PARAM2=3.14)  
<onetick.py.sources.query.query object at ...>
```

Creating python wrapper function around query from `.otq` file:

```
>>> def add_w(src):
...     schema = dict(src.schema)
...     if 'W' in schema:
...         raise ValueError("column 'W' already exists")
...     else:
...         schema['W'] = str
...     q = otp.query('add_w.otq::w', otp.query.config(output_columns=list(schema.items())))
...     return src.apply(q)
>>> t = otp.Tick(A=1)
>>> t = add_w(t)
>>> t.schema
{'A': <class 'int'>, 'W': <class 'str'>}
>>> t['X'] = t['W'].str.upper()
>>> otp.run(t)
        Time  A      W      X
0 2003-12-01  1  hello  HELLO
```

* **Raises:**
  **ValueError****,** **TypeError** – 

#### ``class config(output_columns=None)``

Bases: ``object``

The config allows to specify different query options.

* **Parameters:**
  **output_columns** (*str* *,* *list* *,* *dict* *,* *optional*) – 

  The parameter defines what the outputs columns are.
  Default value is `None` that means no output fields after applying query
  for every output pin.

  The `input` string value means that output columns are the same as inputs for
  every output pin.

  A list of tuples allows to define output columns with their types;
  for example `[('x', int), ('y', float), ...]`. Applicable for every output
  pin.

  A dict allows to specify output columns for every output pin.

##### Examples

```
>>> otp.query('/otqs/some.otq::some_query', otp.query.config(output_columns=[('X': int)]))  
```

* **Raises:**
  **TypeError****,** **ValueError** – 

#### \_\_call_\_(*ticks, **pins)

Return object representing outputs of the query.
This object can be used to get a specified output pin of the query as a new ``onetick.py.Source``.

##### Examples

```
>>> query = otp.query('/otqs/some.otq::some_query', PARAM1='val1')  
>>> query()['OUT']  
<onetick.py.core.source.Source at ...>
```

#### ``to_eval_string()``

Converts query object to OneTick’s eval string.

#### ``update_params(**new_params)``

Update dictionary of parameters of the query.

#### ``property str_params``

Query parameters converted to OneTick string representation.
