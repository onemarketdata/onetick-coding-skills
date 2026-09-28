# Session: configuring OneTick databases and ACL

`onetick-py` provides flexible tools for managing sessions and importing both existing and temporary databases.

## Creating session

Session could be created via ``otp.Session`` class object.

```
session = otp.Session()
# make required queries
session.close()
```

If you want override default temporary config, you can either pass path to config file or
``otp.Config`` object as ``otp.Session`` `config`
constructor parameter.

```
config = otp.Config()
session = otp.Session(config)
```

To avoid manually closing session, you can create it using context manager.

```
with otp.Session(config) as session:
    # make required queries
```

## Setting up ACL

By default, a temporary generated `otp.session.ACL` object is created for every
``otp.Config`` and respectively for each session.

However you could pass path to ACL configuration file if you need to load custom ACL.

```
acl = otp.session.ACL('path/to/acl/config')
config = otp.Config(acl=acl)
session = otp.Session(config)
```

You can also add entities to the ACL by using `otp.session.ACL.add` method or
remove entities using `otp.session.ACL.remove`.

```
session.acl.add(otp.session.ACL.User('new_user'))
session.acl.remove(otp.session.ACL.User('old_user'))
```

## Creating temporary database

To create and add a temporary database to the locator, just create an ``otp.DB`` object and
pass it to the ``otp.Session.use`` method.

```
>>> db = otp.DB('DB_NAME')
>>> session.use(db)  
```

To add data to temporary database use ``otp.DB.add`` method:

```
>>> db.add(otp.Ticks(A=[1, 2, 3]), date=otp.dt(2003, 1, 1), symbol='SYM', tick_type='TT')
```

Alternatively, if you already have the data you want to add to the database, you could pass
``onetick.py.Source`` object as ``onetick.py.DB`` constructor second parameter:

```
>>> data = otp.Ticks(A=[1, 2, 3])
>>> db = otp.DB('DB_NAME', data)
>>> session.use(db)  
```

In fact, this is the only way to initialize temporary database with a raw `Pandas` dataframe.

## Working with existing databases

Adding an existing database to the locator almost the same, as for temporary database.
However, you need to specify locations to load database from via `db_locations` parameter.

```
>>> db = otp.DB('NEW_DB', db_locations=[{'location': '/home/user/data/NEW_DB'}])
>>> session.use(db)  
```

Additional locator configuration variables could be set via `db_locations` and `db_properties` parameters,
for `location` and `db` sections of database description in a locator configuration file correspondingly.

```
>>> db = otp.DB(
...     'TEST_DB',
...     db_properties={
...         'symbology': 'SYM',
...         'tick_timestamp_type': 'NANOS',
...     },
...     db_locations=[{
...         'access_method': otp.core.db_constants.access_method.FILE,
...         'location': '/path/to/test_db/',
...         'start_time': datetime(year=2003, month=1, day=1),
...         'end_time': datetime(year=2023, month=1, day=1),
...     }],
... )
```

See `OneTick Locator Variables` OneTick documentation for available locator configuration variables.

## Derived databases

Derived databases could be added to the locator like a regular database.
Of course, a parent database must be added to create a derived database.

```
>>> db = otp.DB('DB_NAME')
>>> session.use(db)  
>>> derived_db = otp.DB('DB_NAME//DERIVED_LABEL')
>>> session.use(derived_db)  
```

You can also add data to derived database.

```
>>> data = otp.Ticks(A=[1, 2, 3])
>>> derived_db = otp.DB('DB_NAME//DERIVED_LABEL')
>>> session.use(derived_db)  
>>> derived_db.add(data)  
```

See `Derived Databases` OneTick documentation for more info about derived databases.

## Useful types of sessions

There are some other types of session classes,
that are inherited from base ``otp.Session`` class,
but provide some additional functionality.

### otp.TestSession

``otp.TestSession`` sets up some default onetick.py configuration values
and is useful for the purposes of quickly setting up environment to test some simple queries.

### onetick.hosted.Session

`onetick.hosted.Session` automatically scans directory structure on the local machine
finding all OneTick databases, and creating OneTick locator that allows to access them
without the need of additional configuration.

`onetick.hosted` is a separate module located in the
`onetick-hosted` project
on our Gitlab server.
You can find all usage instructions and some examples in the
`README.md` file.

`onetick.hosted` can be installed with `pip`:

```
pip install onetick-hosted
```

## Creating session with different contexts



In OneTick context is a namespace for the databases.

Different contexts allow having sets of databases from different places, local or remote,
and easily switching context with parameter `context` supported by many onetick-py functions.

Default context is named **DEFAULT** and is created automatically by ``otp.Session``.
You can see it by reading the configuration file and seeing **DB_LOCATOR.DEFAULT** variable:

```
>>> session = otp.Session()   
>>> with open(session.config.path) as r:   
...     print(r.read())   
ONE_TICK_CONFIG.ALLOW_ENV_VARS=Yes
...
ACCESS_CONTROL_FILE="/tmp/test_onetick/run_20250127_160920_16360/beige-malkoha.acl"
DB_LOCATOR.DEFAULT="/tmp/test_onetick/run_20250127_160920_16360/lurking-frigatebird.locator"
...
```

Default context can be modified with parameter `locator` of ``otp.Config``.
Additional contexts can be created by adding other *DB_LOCATOR.* variables to OneTick configuration file.
Let’s create context **OTHER**, and create databases in both contexts:

```
>>> default_locator = otp.Locator()  
>>> default_locator.add(otp.DB('A', otp.Tick(A=1), tick_type='TT', symbol='S'))  
>>> other_locator = otp.Locator(empty=True)  
>>> other_locator.add(otp.DB('B', otp.Tick(B=2), tick_type='TT', symbol='S'))  
>>> config = otp.Config(locator=default_locator,  
...                     variables={'DB_LOCATOR.OTHER': other_locator.path})
>>> session = otp.Session(config)  
>>> with open(session.config.path) as r:  
...     print(r.read())  
ONE_TICK_CONFIG.ALLOW_ENV_VARS=Yes
...
ACCESS_CONTROL_FILE="/tmp/test_onetick/run_20250127_160920_16360/ultra-inchworm.acl"
DB_LOCATOR.DEFAULT="/tmp/test_onetick/run_20250127_160920_16360/infrared-crane.locator"
DB_LOCATOR.OTHER="/tmp/test_onetick/run_20250127_160920_16360/tangerine-earthworm.locator"
...
```

After that both contexts can be used when running queries, thus making databases from different locators available:

```
>>> data = otp.DataSource('A', tick_type='TT', symbols='S', schema_policy='manual')  
>>> # running query without parameter *context* will run the query in **DEFAULT** context
>>> print(otp.run(data))  
        Time  A
0 2003-12-01  1
>>> data = otp.DataSource('B', tick_type='TT', symbols='S', schema_policy='manual')  
>>> print(otp.run(data, context='OTHER'))  
        Time  B
0 2003-12-01  2
```

Some other functions also have parameter `context`, e.g. ``otp.databases``:

```
>>> otp.databases()  
{'A': <onetick.py.db._inspection.DB at 0x7f520daa4160>,
 'COMMON': <onetick.py.db._inspection.DB at 0x7f520daa4280>,
 'DEMO_L1': <onetick.py.db._inspection.DB at 0x7f520daa4400>}
>>> otp.databases(context='OTHER')  
{'B': <onetick.py.db._inspection.DB at 0x7f52811c07f0>}
```
