# otp.Operation.str.replace

### ``replace(pat, repl)``

Search for occurrences (case dependent) of `pat` and replace with `repl`.

* **Parameters:**
  * **pat** (*str* *or* *Column* *or* *Operation*) – Pattern to replace.
  * **repl** (*str* *or* *Column* *or* *Operation*) – Replacement string.
* **Returns:**
  String with `pat` replaced by `repl`.
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks(X=['A Table', 'A Chair', 'An Apple'])
>>> data['Y'] = data['X'].str.replace('A', 'The')
>>> otp.run(data)
                     Time         X             Y
0 2003-12-01 00:00:00.000   A Table     The Table
1 2003-12-01 00:00:00.001   A Chair     The Chair
2 2003-12-01 00:00:00.002  An Apple  Then Thepple
```

Other columns can be used as parameters too:

```
>>> data = otp.Ticks(X=['A Table', 'A Chair', 'An Apple'],
...                  PAT=['A', 'A', 'An'],
...                  REPL=['The', 'Their', 'My'])
>>> data['Y'] = data['X'].str.replace(data['PAT'], data['REPL'])
>>> otp.run(data)
                     Time         X PAT   REPL            Y
0 2003-12-01 00:00:00.000   A Table   A    The    The Table
1 2003-12-01 00:00:00.001   A Chair   A  Their  Their Chair
2 2003-12-01 00:00:00.002  An Apple  An     My     My Apple
```
