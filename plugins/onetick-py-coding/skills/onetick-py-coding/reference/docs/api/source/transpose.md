# otp.Source.transpose

#### ``Source.transpose(inplace=False, direction='rows', n=None)``

Data transposing.
The main idea is joining many ticks into one or splitting one tick to many.

* **Parameters:**
  * **inplace** (*bool* *,* *default=False*) – if True method will modify current object,
    otherwise it will return modified copy of the object.
  * **direction** ( *'rows'* *,*  *'columns'* *,* *default='rows'*) – 
    - rows: join certain input ticks (depending on other parameters) with preceding ones.
      : Fields of each tick will be added to the output tick and their names will be suffixed
        with **\_K** where **K** is the positional number of tick (starting from 1) in reverse order.
        So fields of current tick will be suffixed with **\_1**, fields of previous tick will be
        suffixed with **\_2** and so on.
    - columns: the operation is opposite to rows. It splits each input tick to several
      : output ticks. Each input tick must have fields with names suffixed with **\_K**
        where **K** is the positional number of tick (starting from 1) in reverse order.
  * **n** (*Optional* **[*int* *]* *,* *default=None*) – must be specified only if `direction` is ‘rows’.
    Joins every **n** number of ticks with **n-1** preceding ticks.
  * **self** (*Source*)
* **Returns:**
  * If `inplace` parameter is True method will return None,
  * *otherwise it will return modified copy of the object.*
* **Return type:**
  `Source` | None

##### Examples

Merging two ticks into one.

```
>>> data = otp.Ticks(dict(A=[1, 2],
...                       B=[3, 4]))
>>> data = data.transpose(direction='rows', n=2)
>>> otp.run(data)
                     Time             TIMESTAMP_1  A_1  B_1 TIMESTAMP_2  A_2  B_2
0 2003-12-01 00:00:00.001 2003-12-01 00:00:00.001    2    4  2003-12-01    1    3
```

And splitting them back into two.

```
>>> data = data.transpose(direction='columns')
>>> otp.run(data)
                     Time  A  B
0 2003-12-01 00:00:00.000  1  3
1 2003-12-01 00:00:00.001  2  4
```

##### SEE ALSO
**TRANSPOSE** OneTick event processor
