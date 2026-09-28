# otp.Session

### ``class Session(config=None, clean_up=utils.default, copy=True, override_env=False, redirect_logs=True, gather_performance_metrics=False)``

Bases: ``object``

A class for setting up working OneTick session. It keeps configuration files during
the session and allows to manage them. When instance is out of scope, then instance
cleans up config files and configuration.
You can leave the scope manually with method ``close()``.
Also, session is closed automatically if this object is used as a context manager.

#### NOTE
It is allowed to have only one alive session instance in the process.

If you don’t use Session’s instance, then `ONE_TICK_CONFIG` environment variable
should be set to be able to work with OneTick.

If config file is not set then temporary is generated.
Config includes locator and acl file, and if they are not set, then they are generated.

* **Parameters:**
  * **config** (str, ``onetick.py.session.Config``, optional) – Path to an existing OneTick config file; if it is not set, then config will be generated.
    If config is not set, then temporary config is generated. Default is None.
  * **clean_up** (*bool* *,* *optional*) – 

    A flag to control cleaning up process: if it is True then all temporary generated files will
    be automatically removed. It is helpful for debugging. The flag affects only generated files, but
    does not externally passed.

    By default,
    ``otp.config.clean_up_tmp_files`` is used.
  * **copy** (*bool* *,* *optional*) – A flag to control file copy process: if it is True then all externally passed files will be
    copied before usage, otherwise all modifications during an existing session happen directly with
    passed config files. NOTE: we suggest to set this flag only when you fully understand it’s effect.
    Default is True.
  * **override_env** (*bool* *,* *optional*) – 

    If flag is True, then unconditionally `ONE_TICK_CONFIG` environment variable will be overridden
    with a config that belongs to a Session. Otherwise `ONE_TICK_CONFIG`
    will be defined in the scope of session only when it is not defined externally.
    For example, it is helpful when you test ascii_loader that uses ‘ONE_TICK_CONFIG’ only.

    Default is False ( default is False, because overriding external environment variable
    might be not obvious and desirable )
  * **redirect_logs** (*bool* *,* *optional*) – If flag is True, then OneTick logs  will be redirected into a temporary log file. Otherwise
    logs will be mixed with output. Default is True.
  * **gather_performance_metrics** (*bool* *,* *optional*) – 

    If flag is True, then enables performance metrics gathering, by setting `DUMP_PERF_METRICS` config parameter.
    Sets `redirect_logs` flag to `True`.

    Metrics are available after closing a session via `session.performance_metrics` property.

    #### WARNING
    Due to current limitations, metrics are cumulative. So if you run multiple queries in the same process
    (even with different session objects), you’ll get metrics for the whole process since it’s start
    till end of a session with `gather_performance_metrics=True`.

    To avoid this, you can create a session in a separate process, either by using Python’s `multiprocessing`
    or by moving required code to a separate Python script and running it in a new process.

    #### NOTE
    Metrics are gathered for all operations in the session between its creation and closing.

##### Examples

If session is defined with environment, OneTick can be used right away:

```
>>> 'ONE_TICK_CONFIG' in os.environ
True
>>> list(otp.databases())  
[..., 'US_COMP_SAMPLE', ...]
>>> data = otp.DataSource('US_COMP_SAMPLE', symbol='AAPL', tick_type='TRD')
>>> data = data[['PRICE']][:3]
>>> otp.run(data, date=otp.dt(2024, 2, 1))
                           Time   PRICE
0 2024-02-01 04:00:00.008283417  186.50
1 2024-02-01 04:00:00.008290927  185.59
2 2024-02-01 04:00:00.008291153  185.49
```

Collecting performance metrics with `gather_performance_metrics` parameter:

```
>>> with otp.Session(gather_performance_metrics=True) as session:    
...    data_a = otp.DataSource('DB_A', symbol='S1', tick_type='TT')  
...    data_b = otp.DataSource('DB_B', symbol='S1', tick_type='TT')  
...    _ = otp.run(otp.merge([data_a, data_b]))                      
```

```
>>> session.performance_metrics                                      
{
    'user_time': {'name': 'User Time', 'value': 3.39063, 'units': 's'},
    'system_time': {'name': 'System Time', 'value': 1.07813, 'units': 's'},
    'elapsed_time': {'name': 'Elapsed Time', 'value': 6.78816, 'units': 's'},
    'virtual_memory': {'name': 'Virtual Memory', 'value': 6261944320, 'units': 'bytes'},
    'virtual_memory_peak': {'name': 'Virtual Memory Peak', 'value': 6271926272, 'units': 'bytes'},
    'working_set': {'name': 'Working Set', 'value': 228110336, 'units': 'bytes'},
    'working_set_peak': {'name': 'Working Set Peak', 'value': 228126720, 'units': 'bytes'},
    'disk_read': {'name': 'Disk Read', 'value': 32289438, 'units': 'bytes'},
    'disk_write': {'name': 'Disk Write', 'value': 172906, 'units': 'bytes'}
}
```

#### ``use(*items)``

Makes DB or TS available inside the session.

* **Parameters:**
  **items** (``DB`` or ``RemoteTS`` objects) – Items to be added to session.

##### Examples

(note that `session` is created before this example)

```
>>> list(otp.databases()) 
[..., 'US_COMP_SAMPLE', ...]
>>> new_db = otp.DB('ZZZZ')
>>> session.use(new_db)
>>> list(otp.databases()) 
[..., 'US_COMP_SAMPLE', ..., 'ZZZZ', ...]
```

#### ``use_stub(stub_name)``

Adds stub-DB into the session.
The shortcut for `.use(otp.DB(stub_name))`

* **Parameters:**
  **stub_name** (*str*) – name of the stub

#### ``close()``

Close session

#### ``property config``

A reference to the underlying Config object that represents OneTick config file.

* **Return type:**
  ``onetick.py.session.Config``

#### ``property acl``

A reference to the underlying ACL object that represents OneTick access control list file.

* **Return type:**
  ``onetick.py.session.ACL``

#### ``property locator``

A reference to the underlying Locator that represents OneTick locator file.

* **Return type:**
  ``onetick.py.session.Locator``

#### ``property license``

#### ``property ts_databases``

#### ``property databases``

#### ``property performance_metrics``
