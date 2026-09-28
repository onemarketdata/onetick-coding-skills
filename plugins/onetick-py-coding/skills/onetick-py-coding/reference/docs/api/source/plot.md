# otp.Source.plot (jupyter)

#### ``Source.plot(y, x='Time', kind='line', **kwargs)``

Executes the query with known properties and builds a plot resulting dataframe.

Uses the `pandas.DataFrame.plot` method to plot data.
Other parameters could be specified through the `kwargs`.

* **Parameters:**
  * **x** (*str*) – Column name for the X axis
  * **y** (*str*) – Column name for the Y axis
  * **kind** (*str*) – The kind of plot
  * **self** (*Source*)

##### Examples

```
>>> data = otp.Ticks(X=[1, 2, 3])
>>> data.plot(y='X', kind='bar')  
```
