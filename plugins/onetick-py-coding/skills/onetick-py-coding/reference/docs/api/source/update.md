# otp.Source.update

#### ``Source.update(if_set, else_set=None, where=1, inplace=False)``

Update field of the Source or state variable.

* **Parameters:**
  * **if_set** (*dict*) – Dictionary <field name>: <expression>.
  * **else_set** (*dict* *,* *optional*) – Dictionary <field name>: <expression>
  * **where** (*expression* *,* *optional*) – 

    Condition of updating.

    If `where` is True the fields from `if_set` will be updated with corresponding expression.

    If `where` is False, the fields from `else_set` will be updated with corresponding expression.
  * **inplace** (*bool*) – A flag controls whether operation should be applied inplace.
    If `inplace=True`, then it returns nothing. Otherwise method
    returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`.

##### Examples

Columns can be updated with this method:

```
>>> t = otp.Ticks({'X': [1, 2, 3],
...                'Y': [4, 5, 6],
...                'Z': [1, 0, 1]})
>>> t = t.update(if_set={'X': t['X'] + t['Y']},
...              else_set={'X': t['X'] - t['Y']},
...              where=t['Z'] == 1)
>>> otp.run(t)
                     Time  X  Y  Z
0 2003-12-01 00:00:00.000  5  4  1
1 2003-12-01 00:00:00.001 -3  5  0
2 2003-12-01 00:00:00.002  9  6  1
```

State variables can be updated too:

```
>>> t = otp.Ticks({'X': [1, 2, 3],
...                'Y': [4, 5, 6],
...                'Z': [1, 0, 1]})
>>> t.state_vars['X'] = 0
>>> t = t.update(if_set={t.state_vars['X']: t['X'] + t['Y']},
...              else_set={t.state_vars['X']: t['X'] - t['Y']},
...              where=t['Z'] == 1)
>>> t['UX'] = t.state_vars['X']
>>> otp.run(t)
                     Time  X  Y  Z  UX
0 2003-12-01 00:00:00.000  1  4  1   5
1 2003-12-01 00:00:00.001  2  5  0  -3
2 2003-12-01 00:00:00.002  3  6  1   9
```

##### SEE ALSO
**UPDATE_FIELD** and **UPDATE_FIELDS** OneTick event processors
