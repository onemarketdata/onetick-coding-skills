# Accessing a Lower Level API

Some of OneTick’s functionality may not be explicitly added to `onetick-py` but can be access through a lower-level API called `onetick.query`.
See OneTick server documentation for details.

```python
import onetick.py as otp
from onetick.py.otq import otq

trd = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')

trd.sink(otq.Variance(input_field_name='PRICE', output_field_name='VAR_PRICE', bucket_interval=60))
trd = trd.table(VAR_PRICE=float, strict=False) # need to explicitly add the fields added by the `sink` methods to the schema

trd['std'] = otp.math.sqrt(trd['VAR_PRICE'])

otp.run(trd, start=otp.dt(2024, 2, 1, 10), end=otp.dt(2024, 2, 1, 16), symbols='AA')
```
