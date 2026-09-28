# otp.Operation.dtype

#### ``property Operation.dtype``

Returns the type of the column or operation.

##### Examples

```
>>> t = otp.Tick(A=1, B=2.3, C='3')
>>> t['TIMESTAMP'].dtype
<class 'onetick.py.types.nsectime'>
>>> t['A'].dtype
<class 'int'>
>>> t['B'].dtype
<class 'float'>
>>> t['C'].dtype
<class 'str'>
```

##### SEE ALSO
``Source.schema``
