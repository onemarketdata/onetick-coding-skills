# Available tick types for a given database

Following code illustrates how to get available tick types for the US_OPTIONS database

```python
import onetick.py as otp

dbs = otp.databases()
tick_types = dbs['US_OPTIONS'].tick_types()
```

# Available tick types for a given database and on a date

Following code shows how to get all available tick types for a certain date, for example 2022/3/1

```python
import onetick.py as otp

us_comp_db = otp.databases()['US_COMP']
available_tick_types = us_comp_db.tick_types(date=otp.dt(2022, 3, 1))
```

# Get schema for a given database

Following code shows how to get schema for the US_COMP database 

```python
import onetick.py as otp

us_comp_db = otp.databases()['US_COMP']
# returns schema for tick type ='TRD' on the last date
schema = us_comp_db.schema(tick_type='TRD')
```

# Get schema for a given database and on a date

Following code shows how to get schema for the US_COMP database for some date, for example 2023/4/8

```python
import onetick.py as otp

us_comp_db = otp.databases()['US_COMP']
schema_for_date = us_comp_db.schema(tick_type='TRD', date=otp.dt(2023, 4, 8))
```
