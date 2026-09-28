# otp.format

### ``format(format_line, *args, **kwargs)``

Perform a string formatting operation.
Currently, there are only 2 types of formatting available:

1. Float precision - `{:.xf}`, where `x` is number, e.g. `{:.5f}`
2. Time formatting - the same as in `Source.dt.strftime`

See examples for more information.

* **Parameters:**
  * **format_line** (*str*) – String which contains literal text or replacement fields delimited by braces {}.
    Currently content of the braces is not supported.
  * **args** – Values to paste into the line.
  * **kwargs** – Key-word values to paste into the line.
* **Return type:**
  ``Operation`` with type equal to `varstring`

##### Examples

It allows to format ``Operation``. For example, ``Column``:

```
>>> data = otp.Ticks(A=[1, 2], B=['abc', 'def'])
>>> data['C'] = otp.format('A field value is `{}` and B field value is `{}`', data['A'], data['B'])
>>> otp.run(data)
                     Time  A    B                                                C
0 2003-12-01 00:00:00.000  1  abc  A field value is `1` and B field value is `abc`
1 2003-12-01 00:00:00.001  2  def  A field value is `2` and B field value is `def`
```

Formatting can use positional arguments:

```
>>> data = otp.Ticks(A=[1, 2], B=['abc', 'def'])
>>> data['C'] = otp.format('A is `{0}`, B is `{1}`. Also, A is `{0}`', data['A'], data['B'])
>>> otp.run(data)
                     Time  A    B                                     C
0 2003-12-01 00:00:00.000  1  abc  A is `1`, B is `abc`. Also, A is `1`
1 2003-12-01 00:00:00.001  2  def  A is `2`, B is `def`. Also, A is `2`
```

Formatting can be used with key-word arguments:

```
>>> data = otp.Ticks(A=[1, 2], B=['abc', 'def'])
>>> data['C'] = otp.format('A is `{a}`, B is `{b}`. Also, A is `{a}`', a=data['A'], b=data['B'])
>>> otp.run(data)
                     Time  A    B                                     C
0 2003-12-01 00:00:00.000  1  abc  A is `1`, B is `abc`. Also, A is `1`
1 2003-12-01 00:00:00.001  2  def  A is `2`, B is `def`. Also, A is `2`
```

Float numbers can be formatted:

```
>>> data = otp.Ticks(A=[12.3456, 67.8971])
>>> data['B'] = otp.format('A is about {:.2f}', data['A'])
>>> otp.run(data)
                     Time        A                 B
0 2003-12-01 00:00:00.000  12.3456  A is about 12.35
1 2003-12-01 00:00:00.001  67.8971  A is about 67.90
```

Time can be formatted:

```
>>> data = otp.Tick(A=otp.datetime(2020, 4, 5, 17, 56, 3, 789123))
>>> data['B'] = otp.format('A is {:%Y/%m/%d %H:%M:%S.%J}', data['A'])
>>> otp.run(data)
        Time                          A                                   B
0 2003-12-01 2020-04-05 17:56:03.789123  A is 2020/04/05 17:56:03.789123000
```
