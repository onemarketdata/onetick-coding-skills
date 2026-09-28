# otp.Source.sink

#### ``Source.sink(ep, out_pin=None, inplace=True)``

Appends `ep` node to this source (inplace by default).
Connects `out_pin` of this source to `ep`.

Can be used to connect onetick.query objects to ``onetick.py.Source``.

Data schema changes (added or deleted columns) will not be detected automatically
after applying this function, so the user must change the schema himself
by updating ``onetick.py.Source.schema()`` property.

* **Parameters:**
  * **ep** (*otq.graph_components.EpBase* *,*            *otq.graph_components.EpBase.PinnedEp* *,*            *tuple* ***otq.graph_components.EpBase* *,* *uuid.uuid4* *,* *Optional* *[*[*str* *]* *,* *Optional* **[*str* *]* *]*) – onetick.query EP object to append to source.
  * **out_pin** (*Optional* **[*str* *]* *,* *default=None*) – name of the out pin to connect to `ep`
  * **inplace** (*bool* *,* *default=True*) – if True method will modify current object,
    otherwise it will return modified copy of the object.
* **Returns:**
  Returns `None` if `inplace=True`.
* **Return type:**
  ``Source`` or `None`

##### Examples

Adding column ‘B’ directly with onetick.query EP.

```
>>> data = otp.Tick(A=1)
>>> data.sink(otq.AddField(field='B', value=2))
>>> otp.run(data)
        Time  A  B
0 2003-12-01  1  2
```

But we can’t use this column with onetick.py methods yet:

```
>>> data['C'] = data['B']
Traceback (most recent call last):
 ...
AttributeError: There is no 'B' column
```

We should manually change source’s schema:

```
>>> data.schema.update(B=int)
>>> data['C'] = data['B']
>>> otp.run(data)
        Time  A  B  C
0 2003-12-01  1  2  2
```

Use parameter `inplace=False` to return modified copy of the source:

```
>>> data = otp.Tick(A=1)
>>> new_data = data.sink(otq.AddField(field='B', value=2), inplace=False)
>>> otp.run(data)
        Time  A
0 2003-12-01  1
>>> otp.run(new_data)
        Time  A  B
0 2003-12-01  1  2
```

##### SEE ALSO
``onetick.py.Source.schema``, ``onetick.py.core._source.schema.Schema``
