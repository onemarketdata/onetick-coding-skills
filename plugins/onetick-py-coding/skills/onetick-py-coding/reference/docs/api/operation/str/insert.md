# otp.Operation.str.insert

### ``insert(start, length, value)``

Returns a string where `length` characters have been deleted from string,
beginning at `start`, and where `value` has been inserted into string, beginning at `start`.

* **Parameters:**
  * **start** (*int* *or* *Column* *or* *Operation*) – Position to remove from and to insert into.
  * **length** (*int* *or* *Column* *or* *Operation*) – Number if characters to remove.
  * **value** (*str* *or* *Column* *or* *Operation*) – String to insert.

##### Examples

```
>>> data = otp.Ticks(X=['aaaaaaa', 'bbbbb', 'cccc'], Y=['ddd', 'ee', 'f'])
>>> data['INSERTED_1'] = data['X'].str.insert(3, 1, 'X')
>>> data['INSERTED_2'] = data['X'].str.insert(3, 2, 'X')
>>> data['INSERTED_Y'] = data['X'].str.insert(3, 2, data['Y'])
>>> otp.run(data)
                     Time        X    Y INSERTED_1 INSERTED_2 INSERTED_Y
0 2003-12-01 00:00:00.000  aaaaaaa  ddd    aaXaaaa     aaXaaa   aadddaaa
1 2003-12-01 00:00:00.001    bbbbb   ee      bbXbb       bbXb      bbeeb
2 2003-12-01 00:00:00.002     cccc    f       ccXc        ccX        ccf
```

It is possible to insert without removal:

```
>>> data = otp.Ticks(X=['aaaaaaa', 'bbbbb', 'cccc'])
>>> data['INSERTED'] = data['X'].str.insert(3, 0, 'X')
>>> otp.run(data)
                     Time        X  INSERTED
0 2003-12-01 00:00:00.000  aaaaaaa  aaXaaaaa
1 2003-12-01 00:00:00.001    bbbbb    bbXbbb
2 2003-12-01 00:00:00.002     cccc     ccXcc
```

It is possible to remove without insertion:

```
>>> data = otp.Ticks(X=['aaaaaaa', 'bbbbb', 'cccc'])
>>> data['REMOVED'] = data['X'].str.insert(3, 2, '')
>>> otp.run(data)
                     Time        X REMOVED
0 2003-12-01 00:00:00.000  aaaaaaa   aaaaa
1 2003-12-01 00:00:00.001    bbbbb     bbb
2 2003-12-01 00:00:00.002     cccc      cc
```
