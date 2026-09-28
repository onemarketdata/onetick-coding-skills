# otp.Source.character_present

#### ``Source.character_present(field, characters, characters_field='', discard_on_match=False, inplace=False)``

Propagates ticks based on whether the value of the field specified by `field` contains a character
in the set of characters specified by `characters`.
Uses **CHARACTER_PRESENT** EP.

* **Parameters:**
  * **field** (str, ``Column``) – Name of the field (must be present in the input tick descriptor).
  * **characters** (*str* *,* *list* **[*str* *]*) – A set of characters that are searched for in the value of the `field`.
    If set as string, works as list of characters.
  * **characters_field** (str, ``Column``) – If specified, will take a current value of that field and append it to `characters`, if any.
  * **discard_on_match** (*bool*) – When set to `True` only ticks that did not match the filter are propagated,
    otherwise ticks that satisfy the filter condition are propagated.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise, method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Select ticks that have the N or T in EXCHANGE field:

```
>>> data = otp.DataSource('TEST_DATABASE', tick_type='TRD', symbols='A')  
>>> data = data[['PRICE', 'SIZE', 'EXCHANGE']]  
>>> data = data.character_present(field=data['EXCHANGE'], characters='NT')  
>>> otp.run(data)  
                     Time  PRICE   SIZE EXCHANGE
0 2003-12-01 00:00:00.000  28.44  55100        N
1 2003-12-01 00:00:00.001  28.44    100        T
2 2003-12-01 00:00:00.002  28.44    200        T
3 2003-12-01 00:00:00.003  28.45    100        T
4 2003-12-01 00:00:00.004  28.44    500        T
```

Select ticks that have the N or T in EXCHANGE field and character set in OLD_EXCHANGE field:

```
>>> data = otp.DataSource('TEST_DATABASE', tick_type='TRD', symbols='A')  
>>> data = data.character_present(  
...     field=data['EXCHANGE'], characters='NT', characters_field=data['OLD_EXCHANGE'],
... )
>>> data = data[['PRICE', 'SIZE', 'EXCHANGE']]  
>>> otp.run(data)  
                     Time  PRICE   SIZE EXCHANGE
0 2003-12-01 00:00:00.000  28.44  55100        N
1 2003-12-01 00:00:00.001  28.44    100        B
2 2003-12-01 00:00:00.002  28.44    200        B
3 2003-12-01 00:00:00.003  28.45    100        T
4 2003-12-01 00:00:00.004  28.44    200        T
```

##### SEE ALSO
**CHARACTER_PRESENT** OneTick event processor
