# otp.misc.get_username

### ``get_username()``

Returns the string with the name of the user executing the query
and authenticated login name of the user.

* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(USER=otp.get_username())
>>> otp.run(data)  
        Time               USER
0 2003-12-01  onetick (onetick)
```
