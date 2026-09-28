# otp.misc.bit_or

### ``bit_or(value1, value2)``

Performs the logical OR operation on each pair of corresponding bits of the parameters.

* **Parameters:**
  * **value1** (int, ``Operation``, ``Column``)
  * **value2** (int, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

Basic example:

```
>>> data = otp.Tick(A=1)
>>> data['OR'] = otp.bit_or(2, 1)
>>> otp.run(data)
        Time  A  OR
0 2003-12-01  1   3
```

You can also pass ``Column`` as parameter:

```
>>> data = otp.Tick(A=1)
>>> data['OR'] = otp.bit_or(data['A'], 0)
>>> otp.run(data)
        Time  A  OR
0 2003-12-01  1   1
```

Or use ``Operation`` as parameter:

```
>>> data = otp.Tick(A=1)
>>> data['OR'] = otp.bit_or(data['A'] * 2, 3)
>>> otp.run(data)
        Time  A  OR
0 2003-12-01  1   3
```
