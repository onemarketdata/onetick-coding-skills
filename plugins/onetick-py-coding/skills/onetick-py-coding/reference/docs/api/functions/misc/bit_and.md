# otp.misc.bit_and

### ``bit_and(value1, value2)``

Performs the logical AND operation on each pair of corresponding bits of the parameters.

* **Parameters:**
  * **value1** (int, ``Operation``, ``Column``)
  * **value2** (int, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

Basic example:

```
>>> data = otp.Tick(A=1)
>>> data['AND'] = otp.bit_and(2, 3)
>>> otp.run(data)
        Time  A  AND
0 2003-12-01  1    2
```

You can also pass ``Column`` as parameter:

```
>>> data = otp.Tick(A=1)
>>> data['AND'] = otp.bit_and(data['A'], 1)
>>> otp.run(data)
        Time  A  AND
0 2003-12-01  1    1
```

Or use ``Operation`` as parameter:

```
>>> data = otp.Tick(A=1)
>>> data['AND'] = otp.bit_and(data['A'] * 2, 3)
>>> otp.run(data)
        Time  A  AND
0 2003-12-01  1    2
```
