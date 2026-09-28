# otp.misc.bit_xor

### ``bit_xor(value1, value2)``

Performs the logical XOR operation on each pair of corresponding bits of the parameters.

* **Parameters:**
  * **value1** (int, ``Operation``, ``Column``)
  * **value2** (int, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

Basic example:

```
>>> data = otp.Tick(A=1)
>>> data['XOR'] = otp.bit_xor(0b111, 0b011)
>>> otp.run(data)
        Time  A  XOR
0 2003-12-01  1    4
```

You can also pass ``Column`` as parameter:

```
>>> data = otp.Tick(A=0b001)
>>> data['XOR'] = otp.bit_xor(data['A'], 0b011)
>>> otp.run(data)
        Time  A  XOR
0 2003-12-01  1    2
```

Or use ``Operation`` as parameter:

```
>>> data = otp.Tick(A=0b001)
>>> data['XOR'] = otp.bit_xor(data['A'] * 2, 0b011)
>>> otp.run(data)
        Time  A  XOR
0 2003-12-01  1    1
```
