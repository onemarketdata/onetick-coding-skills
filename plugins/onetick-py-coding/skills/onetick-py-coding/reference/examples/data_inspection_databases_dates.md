# List available databases


```python
import onetick.py as otp

dbs = otp.databases()
```

# List available dates with data for a given database

The following example illustrates how to get list of dates with data for the US_OPTIONS database

```python
import onetick.py as otp

dbs = otp.databases()
dates = dbs['US_OPTIONS'].dates()
```

# Get the last date with data for a given database

The following code illustrates how to get the last date with data in the CME database

```python
import onetick.py as otp

dbs = otp.databases()
last_date = dbs['CME'].last_date
```
