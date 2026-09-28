# otp.misc.hash_code

### ``hash_code(value, hash_type)``

Returns hexadecimal encoded hash code for the specified string with the specified hash function.

* **Parameters:**
  * **value** (str, ``Operation``, ``Column``) – value to calculate hash from
  * **hash_type** (*str*) – 

    one of following hash types:
    * sha_1
    * sha_224
    * sha_256
    * sha_384
    * sha_512
    * lookup3
    * metro_hash_64
    * city_hash_64
    * murmur_hash_64
    * sum_of_bytes
    * default
* **Return type:**
  ``Operation``

#### NOTE
Fixed sized string hash result could differ from the same variable length string due to trailing nulls.

##### Examples

Basic example:

```
>>> data = otp.Tick(A=1)
>>> data['HASH'] = otp.hash_code('some_string', 'sha_224')
>>> otp.run(data)
        Time  A                                                      HASH
0 2003-12-01  1  12d3f96511450121e6343b5ace065ec9de7b2a946b86f7dfab8ac51f
```

You can also pass ``Operation`` as a `value` parameter:

```
>>> data = otp.Tick(A=otp.varstring('some_string'))
>>> data['HASH'] = otp.hash_code(data['A'], 'sha_224')
>>> otp.run(data)
        Time            A                                                      HASH
0 2003-12-01  some_string  12d3f96511450121e6343b5ace065ec9de7b2a946b86f7dfab8ac51f
```

For the same string stored in strings with different fixed sizes, the hash value may differ:

```
>>> test_str = 'example'
>>> data = otp.Tick(A=otp.string`128`)
>>> data['Fixed'] = otp.hash_code(data['A'], 'sha_1')
>>> data['Var'] = otp.hash_code(otp.varstring(test_str), 'sha_1')
>>> otp.run(data)
        Time        A                                     Fixed                                       Var
0 2003-12-01  example  bdab82ec533c09646e45f15dc4e7ad2d2d1a8ff1  c3499c2729730a7f807efb8676a92dcb6f8a3f8f
```

##### SEE ALSO
**COMPUTE_HASH_CODE_STR** OneTick built-in function
