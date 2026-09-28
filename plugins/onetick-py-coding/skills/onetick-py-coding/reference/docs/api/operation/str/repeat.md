# otp.Operation.str.repeat

### ``repeat(repeats)``

Duplicate a string `repeats` times.

* **Parameters:**
  **repeats** (*int* *or* *Column* *or* *Operation*) – Non-negative number of copies of the string.
  Repeating zero times results in empty string.
  Repeating negative number of times results in exception.
* **Returns:**
  String repeated `repeats` times.
* **Return type:**
  `Operation`

#### NOTE
* Alternative for the `repeat` function is multiplication.
* The returned string has the same type and maximum length as the original field.

##### Examples

```
>>> data = otp.Ticks(X=['Banana', 'Ananas', 'Apple'])
>>> data['X'] = data['X'].str.repeat(3)
>>> otp.run(data)
                     Time                   X
0 2003-12-01 00:00:00.000  BananaBananaBanana
1 2003-12-01 00:00:00.001  AnanasAnanasAnanas
2 2003-12-01 00:00:00.002     AppleAppleApple
```

Other columns can be used as parameter `repeats` too:

```
>>> data = otp.Ticks(X=['Banana', 'Ananas', 'Apple'], TIMES=[1, 3, 2])
>>> data['Y'] = data['X'].str.repeat(data['TIMES'])
>>> otp.run(data)
                     Time       X  TIMES                   Y
0 2003-12-01 00:00:00.000  Banana      1              Banana
1 2003-12-01 00:00:00.001  Ananas      3  AnanasAnanasAnanas
2 2003-12-01 00:00:00.002   Apple      2          AppleApple
```

The returned string has the same type and therefore the same maximum length as the original field:

```
>>> data = otp.Ticks(X=`otp.string[9`])
>>> data['Y'] = data['X'].str.repeat(3)
>>> data.schema
{'X': string[9], 'Y': string[9]}
>>> otp.run(data)
        Time       X          Y
0 2003-12-01  Banana  BananaBan
```

`repeat` does the same thing as multiplication by a non-negative int:

```
>>> data = otp.Ticks(X=['Banana'], N=[2])
>>> data['X2'] = data['X'] * data['N']
>>> data['X3'] = data['X'] * 3
>>> otp.run(data)
        Time       X  N            X2                  X3
0 2003-12-01  Banana  2  BananaBanana  BananaBananaBanana
```

Multiplying by 0 results in empty string:

```
>>> data = otp.Ticks(X=['Banana', 'Apple'])
>>> data['Y'] = data['X'].str.repeat(0)
>>> otp.run(data)
                     Time       X Y
0 2003-12-01 00:00:00.000  Banana
1 2003-12-01 00:00:00.001   Apple
```
