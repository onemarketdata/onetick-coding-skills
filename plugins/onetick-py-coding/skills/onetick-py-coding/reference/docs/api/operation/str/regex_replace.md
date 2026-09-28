# otp.Operation.str.regex_replace

### ``regex_replace(pat, repl, *, replace_every=False, caseless=False)``

Search for occurrences (case dependent) of `pat` and replace with `repl`.

* **Parameters:**
  * **pat** (*str* *or* *Column* *or* *Operation*) – Pattern to replace specified via the POSIX extended regular expression syntax.
  * **repl** (*str* *or* *Column* *or* *Operation*) – Replacement string. `\0` refers to the entire matched text. `\1` to `\9` refer
    to the text matched by the corresponding parenthesized group in `pat`.
  * **replace_every** (*bool*) – If `replace_every` flag is set to `True`, all matches will be replaced, if `False` only the first one.
  * **caseless** (*bool*) – If the `caseless` flag is set to `True`, matching is case-insensitive.
* **Returns:**
  String with pattern `pat` replaced by `repl`.
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks(X=['A Table', 'A Chair', 'An Apple'])
>>> data['Y'] = data['X'].str.regex_replace('An? ', 'The ')
>>> otp.run(data)
                     Time         X          Y
0 2003-12-01 00:00:00.000   A Table  The Table
1 2003-12-01 00:00:00.001   A Chair  The Chair
2 2003-12-01 00:00:00.002  An Apple  The Apple
```

Parameter `replace_every` will replace all occurrences of `pat` in the string:

```
>>> data = otp.Ticks(X=['A Table, A Chair, An Apple'])
>>> data['Y'] = data['X'].str.regex_replace('An? ', 'The ', replace_every=True)
>>> otp.run(data)
        Time                           X                                Y
0 2003-12-01  A Table, A Chair, An Apple  The Table, The Chair, The Apple
```

Capturing groups in regular expressions is supported:

```
>>> data = otp.Ticks(X=['11/12/1992', '9/22/1993', '3/30/1991'])
>>> data['Y'] = data['X'].str.regex_replace(r'(\d{1,2})/(\d{1,2})/', r'\2.\1.')
>>> otp.run(data)
                     Time           X           Y
0 2003-12-01 00:00:00.000  11/12/1992  12.11.1992
1 2003-12-01 00:00:00.001   9/22/1993   22.9.1993
2 2003-12-01 00:00:00.002   3/30/1991   30.3.1991
```

##### SEE ALSO
``extract()``
