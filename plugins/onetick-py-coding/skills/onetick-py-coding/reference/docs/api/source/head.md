# otp.Source.head (jupyter)

#### ``Source.head(n=5, **kwargs)``

*Executes the query* and returns first `n` ticks as a pandas dataframe.

It is useful in the Jupyter case when you want to observe first `n` values.

* **Parameters:**
  * **n** (*int* *,* *default=5*) – number of ticks to return
  * **kwargs** – parameters will be passed to ``otp.run``
  * **self** (*Source*)
* **Return type:**
  `DataFrame`

##### Examples

```
>>> data = otp.Ticks(X=list('abcdefgik'))
>>> data.head()[['X']]
    X
0 a
1 b
2 c
3 d
4 e
```

##### SEE ALSO
``onetick.py.agg.first()``

``otp.run``

``onetick.py.Source.tail()``

``onetick.py.Source.count()``

