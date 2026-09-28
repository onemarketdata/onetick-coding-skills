# otp.DB

### ``class DB(name=None, src=None, date=None, symbol=None, tick_type=None, kind='archive', db_properties=None, db_locations=None, db_raw_data=None, db_feed=None, write=True, clean_up=utils.default, destroy_access=False, minimum_start_date=None, maximum_end_date=None)``

A class representing OneTick databases when configuring
``locators`` and ``ACL``.

A database can point to an existing OneTick archive
or the temporary directory can be created with the data provided.

This class object can then be ``used``
in ``onetick.py.Session``.

The data to add to the database can be passed into the
constructor with parameter `src` or later with the ``add()`` method.

Note that after ticks were written to a particular timestamps in the database,
they can’t be updated for the same timestamps.

* **Parameters:**
  * **name** (*str*) – Database name.
    Derived databases are specified in “parent//derived” format.
    A derived database inherits the parent database properties.
  * **src** (``Source``, optional) – Data to add to the database.
  * **date** (``onetick.py.date`` or ``datetime.date``, optional) – `src` data will be added to this date.
    Can be set only if `src` is set.
    Default value is the same as in ``add()`` method.
  * **symbol** (*str* *,* *optional*) – Symbol name to add `src` data for.
    Can be set only if `src` is set.
    Default value is the same as in ``add()`` method.
  * **tick_type** (*str* *,* *optional*) – Tick type to add `src` data for.
    Can be set only if `src` is set.
    Default value is the same as in ``add()`` method.
  * **db_properties** (``dict``, optional) – Properties of the database to add to the locator
  * **db_locations** (``list`` of ``dict``, optional) – A list of locations for the database to add to the locator.
    This parameter is a list, because databases in a locator can have several location sections.
    If not specified, a temporary directory is used as the database location.
  * **db_raw_data** (``list`` of ``dict``, optional) – Raw database configuration.
  * **db_feed** (*dict* *,* *optional*) – Feed configuration.
  * **write** (*bool* *,* *optional*) – Flag that controls access to write to database.
  * **clean_up** (*bool* *,* *optional*) – Flag that controls temporary database cleanup
  * **destroy_access** (*bool* *,* *optional*) – Flag that controls access to destroy the database.
  * **minimum_start_date** (str or ``datetime.date`` or ``onetick.py.date``) – Specifies the minimum date of the tick that can be served to a user.
    The format for the value is *YYYYMMDD*.
    The OneTick server enforces this permission by verifying that
    the query start time is not less than time of 00:00:00, in GMT timezone, for the specified minimum start date.
  * **maximum_end_date** (str or ``datetime.date`` or ``onetick.py.date``) – Specifies the date of the tick, starting from which ticks are not allowed to be returned to a user.
    The format for the value is *YYYYMMDD*.
    The OneTick server enforces this permission by verifying that
    the query end time is less than time of 00:00:00, in GMT timezone, for the specified maximum end date.

#### NOTE
This class can only be used to create database on the local machine.
Database is created by using/creating a directory in the local filesystem
and adding entries to the OneTick locator and ACL local files.
This class can’t be used to manage remote databases.

##### Examples

A database can be initialized along with data:

```
>>> data = otp.Ticks(X=['hello', 'world!'])
>>> db = otp.DB('MYDB', data)
```

Database symbol name, tick type and date to which the data will be written can be changed like this:

```
>>> db = otp.DB('MYDB', data, symbol='S_S', tick_type='T_T', date=otp.dt(2003, 12, 1))
```

You can specify a derived db by using `//` as a separator:

```
>>> data = otp.Ticks(X=['parent1', 'parent2'])
>>> db = otp.DB('DB_A', data)
>>> db.add(data)
```

```
>>> data_derived = otp.Ticks(X=['derived1', 'derived2'])
>>> db_derived = otp.DB('DB_A//DB_D')
>>> session.use(db_derived)
>>> db_derived.add(data_derived)
```

You can add an existing OneTick database to the locator or create a new one:

```
>>> existing_db = otp.DB('MY_DB',  
...                      db_locations=[{'location': '/home/user/data/MY_DB',
...                                     'start_time': datetime(2003, 1, 1),
...                                     'end_time': datetime(2010, 1, 1),
...                                     'day_boundary_tz': 'EST5EDT'}])
>>> session.use(existing_db)  
```

#### ``add(src, date=None, start=None, end=None, symbol=None, tick_type=None, timezone=None, **kwargs)``

Add data to a database.
If ticks with the same timestamp are already presented in database old values won’t be updated.

* **Parameters:**
  * **src** (`otp.Source`) – source that will be written to the database.
  * **date** (*datetime* *or* *None*) – date of the day in which the data will be saved.
    The timestamps of the ticks should be between the start and the end of the day.
    Be default, it is set to otp.config.default_date.
  * **start** (*datetime* *or* *None*) – start day of period in which the data will be saved.
    The timestamps of the ticks should be between start and end dates.
    Cannot be used with date parameter.
    Be default, None.
  * **end** (*datetime* *or* *None*) – end day of period in which the data will be saved.
    The timestamps of the ticks should be between start and end dates.
    Cannot be used with date parameter.
    Be default, None.
  * **symbol** (*str* *or* *Column*) – resulting symbol name string or column to get symbol name from.
    Be default, it is set to otp.config.default_db_symbol.
  * **tick_type** (*str* *or* *Column*) – resulting tick type string or column to get tick type from.
    If tick type is None then an attempt will be taken to get
    tick type name automatically based on the `src` source’s schema.
    (ORDER, QTE, TRD and NBBO tick types are supported).
  * **timezone** (*str*) – This timezone will be used for running the query.
    By default, it is set to otp.config.tz.
  * **kwargs** – other arguments that will be passed to ``onetick.py.Source.write()`` function.

##### Examples

Data is saved to the specified date, symbol and tick type:
(note that `session` is created before this example)

```
>>> db = otp.DB('MYDB2')
>>> db.add(otp.Ticks(A=[4, 5, 6]), date=otp.dt(2003, 1, 1), symbol='SMB', tick_type='TT')
>>> session.use(db)
```

We can get the same data by specifying the same parameters:

```
>>> data = otp.DataSource(db, date=otp.dt(2003, 1, 1), symbols='SMB', tick_type='TT')
>>> otp.run(data)
                     Time  A
0 2003-01-01 00:00:00.000  4
1 2003-01-01 00:00:00.001  5
2 2003-01-01 00:00:00.002  6
```

#### ``property properties``

Get dict of database properties.

* **Return type:**
  `dict`

##### Examples

```
>>> otp.DB('X').properties  
{'symbology': 'BZX',
 'archive_compression_type': 'NATIVE_PLUS_GZIP',
 'tick_timestamp_type': 'NANOS'}
```

#### ``property locations``

Get list of database locations.

* **Return type:**
  `list` of `dict`

##### Examples

```
>>> otp.DB('X').locations 
[{'access_method': 'file',
  'start_time': '20021230000000',
  'end_time': '21000101000000',
  ...}]
```

#### ``property raw_data``

Get dict of database raw configurations.

* **Return type:**
  `dict` of `dict`

##### Examples

```
>>> db = otp.DB('RAW_EXAMPLE',
...     db_raw_data=[{
...         'id': 'PRIMARY_A',
...         'prefix': 'DATA.',
...         'locations': [
...             {'mount': 'mount1'}
...         ]
...     }]
... )
>>> db.raw_data 
[{'id': 'PRIMARY_A', 'prefix': 'DATA.', 'locations': [{'mount': 'mount1', 'access_method': 'file', ...}]}]
```

#### ``property feed``

Get dict of database feed configuration.

* **Return type:**
  `dict`

##### Examples

```
>>> db = otp.DB('RAW_EXAMPLE',
...     db_raw_data=[{
...         'id': 'PRIMARY_A',
...         'prefix': 'DATA.',
...         'locations': [
...             {'mount': 'mount1'}
...         ]
...     }],
...     db_feed={'type': 'rawdb', 'raw_source': 'PRIMARY_A'},
... )
>>> db.feed
{'type': 'rawdb', 'raw_source': 'PRIMARY_A', 'format': 'native'}
```
