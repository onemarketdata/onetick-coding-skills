# Check whether a give date was a holiday in the Cloud DB

It can be done using OQD on the OneTick Cloud in case if a database is there.

In the following example the 'CME' database is there


```python
import onetick.py as otp

def is_holiday(cloud_db: str, date: otp.datetime):
    holidays = otp.DataSource('OQD_MKTCAL', tick_type='MKTCAL')
    holidays, _ = holidays[(holidays['ACTIVITY_NAME'] == 'ALL_HOLIDAY')]
    return len(otp.run(holidays, symbols=f'CLOUD_DB_{cloud_db}', date=date)) > 0

is_holiday('CME', otp.dt(2023, 1, 19))
```
