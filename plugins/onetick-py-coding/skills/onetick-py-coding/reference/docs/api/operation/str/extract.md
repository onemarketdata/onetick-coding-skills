# otp.Operation.str.extract

### ``extract(pat, rewrite='\\\\0', caseless=False)``

Match the string against a regular expression specified by `pat` and return the first match.
The `rewrite` parameter can optionally be used to arrange the matched substrings and embed them within the
string specified in `rewrite`.

* **Parameters:**
  * **pat** (*str* *or* *Column* *or* *Operation*) – Pattern to search for specified via the POSIX extended regular expression syntax.
  * **rewrite** (*str* *or* *Column* *or* *Operation*) – A string that specifies how to arrange the matched text. `\0` refers to the entire matched text.
    `\1` to `\9` refer to the text matched by the corresponding parenthesized group in `pat`.
    `\u` and `\l` modifiers within the `rewrite` string convert the case of the text that
    matches the corresponding parenthesized group (e.g., `\u1` converts `\1` to uppercase).
  * **caseless** (*bool*) – If the `caseless` flag is set to `True`, matching is case-insensitive.
* **Returns:**
  String matched by `pat` with format specified in `rewrite`.
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks(X=['Mr. Smith: +1348 +4781', 'Ms. Smith: +8971'])
>>> data['TEL'] = data['X'].str.extract(r'\+\d{4}')
>>> otp.run(data)
                     Time                       X    TEL
0 2003-12-01 00:00:00.000  Mr. Smith: +1348 +4781  +1348
1 2003-12-01 00:00:00.001        Ms. Smith: +8971  +8971
```

You can specify the group to extract in the `rewrite` parameter:

```
>>> data = otp.Ticks(X=['Mr. Smith: 1992/12/22', 'Ms. Smith: 1989/10/15'])
>>> data['BIRTH_YEAR'] = data['X'].str.extract(r'(\d{4})/(\d{2})/(\d{2})', rewrite=r'birth year: \1')
>>> otp.run(data)
                     Time                      X        BIRTH_YEAR
0 2003-12-01 00:00:00.000  Mr. Smith: 1992/12/22  birth year: 1992
1 2003-12-01 00:00:00.001  Ms. Smith: 1989/10/15  birth year: 1989
```

You can use a column as a `rewrite` or `pat` parameter:

```
>>> data = otp.Ticks(X=['Kelly, Mr. James', 'Wilkes, Mrs. James', 'Connolly, Miss. Kate'],
...                  PAT=['(Mrs?)\\.', '(Mrs?)\\.', '(Miss)\\.'],
...                  REWRITE=['Title 1: \\1', 'Title 2: \\1', 'Title 3: \\1'])
>>> data['TITLE'] = data['X'].str.extract(data['PAT'], rewrite=data['REWRITE'])
>>> otp.run(data)
                     Time                     X       PAT      REWRITE          TITLE
0 2003-12-01 00:00:00.000      Kelly, Mr. James  (Mrs?)\.  Title 1: \1  Title 1:   Mr
1 2003-12-01 00:00:00.001    Wilkes, Mrs. James  (Mrs?)\.  Title 2: \1  Title 2:  Mrs
2 2003-12-01 00:00:00.002  Connolly, Miss. Kate  (Miss)\.  Title 3: \1  Title 3: Miss
```

Case of the extracted string can be changed by adding `l` and `u` to extract group:

```
>>> data = otp.Ticks(NAME=['mr. BroWn', 'Ms. smITh'])
>>> data['RESULT'] = data['NAME'].str.extract(r'(m)([rs]\. )([a-z])([a-z]*)', r'\u1\l2\u3\l4', caseless=True)
>>> otp.run(data)
                     Time       NAME     RESULT
0 2003-12-01 00:00:00.000  mr. BroWn  Mr. Brown
1 2003-12-01 00:00:00.001  Ms. smITh  Ms. Smith
```

##### SEE ALSO
``regex_replace``
