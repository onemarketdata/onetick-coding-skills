# Example of tgypical issues in tests 

What issues do you see in the following piece of code / test ? I see here are 3 issues

```python
import pytest
import onetick.py as otp
import pandas as pd


@pytest.fixture
def session():
    with otp.TestSession():
        yield


@pytest.fixture
def create_ticks():
    ticks = otp.Ticks({
        "TIMESTAMP": [otp.dt(2023, 3, 31, 10), otp.dt(2023, 3, 31, 10, 1)],
        "SECURITYTYPE": ["OPTION", "OPTION"],
        "STATE": ["F", "PF"],
        "QTY_FILLED": [100, 200],
        "BUY_FLAG": [1, 0],
        "PUTORCALL": ["CALL", "PUT"]
    })
    return ticks


def test_prepare_orders(session, create_ticks):
    db = otp.DB("options_db_tt", tick_type="TRD")
    db.add(create_ticks, symbol="OPTIONS")

     # some test logic
     ...
```

## Answer

The provided code has several issues:

1. Session Fixture Not Yielding Session Object:

```python
The session fixture does not yield the session object, which means the session is not accessible within the test function.
Fix: Yield the session object from the fixture.
@pytest.fixture
def session():
    with otp.TestSession() as s:
        yield s
```

2. Database Not Added to Session:

The db object created in the test_prepare_orders function is not added to the session, which means the session does not know about the database.
Fix: Use the session to add the database.

```python
def test_prepare_orders(session, create_ticks):
    db = otp.DB("options_db_tt", tick_type="TRD")
    session.use(db)
    db.add(create_ticks, symbol="OPTIONS")
    # some test logic
    ...
```

3. The 'tick_type' parameter in DB constructor

The 'tick_type' parameter in DB constructor does not make any effect because the 'src' parameter is not specified.

4. The 'TIMESTAMP' field in the otp.Ticks is confusing

The 'TIMESTAMP' field in the otp.Ticks is confusing. The proper way to specify timestamps on ticks is using the
'offset' field, and beginning for offses is specified in the `db.add` method as `date` parameter

```python
@pytest.fixture
def create_ticks():
    ticks = otp.Ticks({
        "TIMESTAMP": [otp.dt(2023, 3, 31, 10), otp.dt(2023, 3, 31, 10, 1)],
        "SECURITYTYPE": ["OPTION", "OPTION"],
        "STATE": ["F", "PF"],
        "QTY_FILLED": [100, 200],
        "BUY_FLAG": [1, 0],
        "PUTORCALL": ["CALL", "PUT"]
    })
    return ticks


def test_prepare_orders(session, create_ticks):
    db = otp.DB("options_db_tt")
    db.add(create_ticks, symbol="OPTIONS", date=otp.dt(2023, 3, 31))
    session.use(db)
    # some test logic
    ...
```
