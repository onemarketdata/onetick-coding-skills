# otp.Source.logf

#### ``Source.logf(message, severity, *args, where=None, inplace=False)``

Call built-in OneTick `LOGF` function.

* **Parameters:**
  * **message** (*str*) – Log message/format string. The underlying formatting engine is the Boost Format Library:
    `https://www.boost.org/doc/libs/1_53_0/libs/format/doc/format.html`
  * **severity** (*str*) – Severity of message. Supported values: `ERROR`, `WARNING` and `INFO`.
  * **where** (*Operation*) – A condition that allows to filter ticks to call `LOGF` on. `None` for no filtering.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing. Otherwise method returns a new modified
    object.
  * **args** (*list*) – Parameters for format string (optional).
  * **self** (*Source*)
* **Return type:**
  ``Source``

##### Examples

```
>>> data = otp.Ticks(PRICE=[97, 99, 103])
>>> data = data.logf("PRICE (%1%) is higher than 100", "WARNING", data["PRICE"], where=(data["PRICE"] > 100))
```
