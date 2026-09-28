# otp.Operation.str.find

### ``find(sub, start=0)``

Find the index of `sub` in the string. If not found, returns `-1`.

* **Parameters:**
  * **sub** (*str* *or* *Column* *or* *Operation*) – Substring to find.
  * **start** (*int* *or* *Column* *or* *Operation*) – Starting position to find.
* **Returns:**
  The starting position of the substring or `-1` if it is not found.
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks(X=['ananas', 'banana', 'potato'])
>>> data['Y'] = data['X'].str.find('ana')
>>> otp.run(data)
                     Time       X  Y
0 2003-12-01 00:00:00.000  ananas  0
1 2003-12-01 00:00:00.001  banana  1
2 2003-12-01 00:00:00.002  potato -1
```

Other columns can be used as parameter `sub` too:

```
>>> data = otp.Ticks(X=['Ananas', 'Banana', 'Potato'], sub=['Ana', 'anan', 'ato'])
>>> data['Y'] = data['X'].str.find(data['sub'])
>>> otp.run(data)
                     Time       X   sub  Y
0 2003-12-01 00:00:00.000  Ananas   Ana  0
1 2003-12-01 00:00:00.001  Banana  anan  1
2 2003-12-01 00:00:00.002  Potato   ato  3
```

Note that empty string will be found at the start of any string:

```
>>> data = otp.Ticks(X=['string', ''])
>>> data['Y'] = data['X'].str.find('')
>>> otp.run(data)
                     Time       X  Y
0 2003-12-01 00:00:00.000  string  0
1 2003-12-01 00:00:00.001          0
```

`start` parameter is used to find `sub` starting from selected position:

```
>>> data = otp.Ticks(X=['ababab', 'abbbbb'])
>>> data['Y'] = data['X'].str.find('ab', 1)
>>> otp.run(data)
                     Time       X  Y
0 2003-12-01 00:00:00.000  ababab  2
1 2003-12-01 00:00:00.001  abbbbb -1
```
