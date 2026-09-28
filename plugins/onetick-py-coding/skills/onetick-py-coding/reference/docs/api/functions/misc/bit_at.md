# otp.misc.bit_at

### ``bit_at(value, index)``

Return bit from `value` at `index` position from the end (zero-based).

* **Parameters:**
  * **value** (int, ``Operation``, ``Column``)
  * **index** (int, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

Basic example:

```
>>> data = otp.Tick(A=1)
>>> data['AT'] = otp.bit_at(0b0010, 1)
>>> otp.run(data)
        Time  A  AT
0 2003-12-01  1   1
```

You can also pass ``Column`` as parameter:

```
>>> data = otp.Tick(A=0b0001)
>>> data['AT'] = otp.bit_at(data['A'], 0)
>>> otp.run(data)
        Time  A  AT
0 2003-12-01  1   1
```

Or use ``Operation`` as parameter:

```
>>> data = otp.Tick(A=0b0001)
>>> data['AT'] = otp.bit_at(data['A'] * 2, 0)
>>> otp.run(data)
        Time  A  AT
0 2003-12-01  1   0
```
