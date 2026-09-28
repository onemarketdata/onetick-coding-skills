# otp.misc.get_onetick_version

### ``get_onetick_version()``

Returns the string with the build name of OneTick.
Build string may have different format depending on OneTick version.

* **Return type:**
  ``Operation``

#### NOTE
The version is returned from the server where the query executes.
Usually it’s the same server where the database specified in ``otp.run`` resides.

##### Examples

```
>>> data = otp.Tick(VERSION=otp.get_onetick_version())
>>> otp.run(data, symbols='US_COMP_SAMPLE::')  
        Time                                      VERSION
0 2003-12-01  BUILD_rel_20250727_update2 (20250727120000)
```
