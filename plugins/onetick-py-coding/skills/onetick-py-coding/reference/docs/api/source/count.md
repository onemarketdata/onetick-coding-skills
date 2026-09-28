# otp.Source.count (jupyter)

#### ``Source.count(**kwargs)``

Returns the number of ticks in the query.

Adds an aggregation that calculate total ticks count, and *executes a query*.
Result is a single value – number of ticks. Possible application is the Jupyter when
a developer wants to check data presences for example.

* **Parameters:**
  * **kwargs** – parameters that will be passed to ``otp.run``
  * **self** (*Source*)
* **Return type:**
  `int`

##### Examples

```
>>> data = otp.Ticks(X=[1, 2, 3])
>>> data.count()
3
```

```
>>> data = otp.Empty()
>>> data.count()
0
```

##### SEE ALSO
``onetick.py.agg.count()``

``otp.run``

``onetick.py.Source.head()``

``onetick.py.Source.tail()``

