# otp.Operaion.astype

#### ``Operation.astype(to_type)``

Alias for the ``apply()`` method with type.

##### Examples

```
>>> data = otp.Tick(A=1, B=2.2, C='3.3')
>>> data['A'] = data['A'].astype(str) + 'A'
>>> data['B'] = data['B'].astype(int) + 1
>>> data['C'] = data['C'].astype(float) + 0.1
>>> otp.run(data)
        Time  B   A    C
0 2003-12-01  3  1A  3.4
```

##### SEE ALSO
``apply()``
