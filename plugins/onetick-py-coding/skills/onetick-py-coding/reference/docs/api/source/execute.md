# otp.Source.execute

#### ``Source.execute(*operations, inplace=False)``

Execute operations without returning their values.
Some operations in onetick.py can be used to modify the state of some object
(tick sequences mostly) and in that case user may not want to save the result of the
operation to column.

* **Parameters:**
  * **operations** (list of ``Operation``) – operations to execute.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing. Otherwise method returns a new modified
    object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A')
>>> data = data.execute(data.state_vars['SET'].erase(A=1))
```

##### SEE ALSO
**EXECUTE_EXPRESSIONS** OneTick event processor
