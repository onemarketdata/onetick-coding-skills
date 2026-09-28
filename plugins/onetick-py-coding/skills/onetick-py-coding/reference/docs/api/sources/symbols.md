# otp.Symbols

### ``class Symbols(db=None, find_params=None, keep_db=False, pattern='%', for_tick_type=None, show_tick_type=False, symbology='', show_original_symbols=False, discard_on_match=None, cep_method=None, poll_frequency=None, symbols_to_return=None, tick_type=utils.adaptive, \_tick_type=utils.adaptive, start=utils.adaptive, end=utils.adaptive, date=None, schema=None, query_parameters=None, **kwargs)``

Bases: ``Source``

Construct a source that returns symbol names from the database.

The **SYMBOL_NAME** field will contain symbol names.

* **Parameters:**
  * **db** (str, ``eval query``) – Name of the database where to search symbols.
    By default the database used by ``otp.run`` will be inherited.
  * **keep_db** (*bool*) – Flag that indicates whether symbols should have a database name prefix in the output.
    If True, symbols are returned in *DB_NAME::SYMBOL_NAME* format.
    Otherwise only symbol names are returned.
  * **pattern** (*str*) – 

    Usual and special characters can be used to search for symbols.
    This parameter uses SQL LIKE syntax which has some special characters:
    * `%`  - any number of any characters (zero too)
    * `_`  - any single character
    * `\` - used to escape special characters

    For example, if you want symbol name starting with `NQ`, you should write `NQ%`.

    If you want symbol name to contain literal `%` or `_` characters, you should write `NQ\%` or `NQ\_`.

    `\` is a special character too, so, if you want symbol name to contain literal backslash,
    it need to be escaped too, e.g.
    to match `NQ\M23` symbol name you need to set pattern `NQ\\M23`.

    #### NOTE
    Python strings are also using `\` as an escape character,
    so in python code you need to escape backslash twice `"NQ\\\\M23"`
    or use python raw strings: `r"NQ\\M23"`.
    See examples below for details.

    Default for this parameter is `%` (any symbol name).
  * **for_tick_type** (*str*) – Fetch only symbols belong to this tick type, if specified.
    Otherwise fetch symbols for all tick types.
  * **show_tick_type** (*bool*) – Add the **TICK_TYPE** column with the information about tick type
  * **symbology** (*str*) – The destination symbology for a symbol name translation.
    Translation is performed, if destination symbology is not empty
    and is different from that of the queried database.
  * **show_original_symbols** (*bool*) – 

    Switches original symbol name propagation as a tick field **ORIGINAL_SYMBOL_NAME**
    if symbol name translation is performed (if parameter `symbology` is set).

    #### NOTE
    If this parameter is set to True, database symbols with missing translations are also propagated.
    In this case **ORIGINAL_SYMBOL_NAME** will be presented, but **SYMBOL_NAME** field will be empty.
  * **discard_on_match** (*bool*) – If True, then parameter `pattern` filters out symbols to return from the database.
  * **cep_method** (*str*) – 

    The method to be used for extracting database symbols in CEP mode.
    Possible values are:
    > * *use_db*: symbols will be extracted from the database with intervals
    >   specified by the `poll_frequency` parameter, and new symbols will be output.
    > * *use_cep_adapter*: CEP adapter will be used to retrieve and propagate the symbols with every heartbeat.
    > * Default: None, the EP will work the same way as for historical queries,
    >   i.e. will query the database for symbols once.
  * **poll_frequency** (*int*) – Specifies the time interval in *minutes* to check the database for new symbols.
    This parameter can be specified only if `cep_method` is set to *use_db*.
    The minimum value is 1 minute.
  * **symbols_to_return** (*str*) – 

    Indicates whether all symbols must be returned or only those which are in the query time range.
    Possible values are:
    > * *all_in_db*: All symbols are returned.
    > * *with_tick_in_query_range*: Only the symbols which have ticks in the query time range are returned.
    >   This option is allowed only when `cep_method` is set to *use_cep_adapter*.

    Default is *all_in_db*.
  * **\_tick_type** (*str*) – Custom tick type for the node of the graph.
    By default “ANY” tick type will be set.
  * **tick_type** (*str*) – 

    #### ATTENTION
    This parameter is deprecated, use parameter `_tick_type` instead.
    Do not confuse this parameter with `for_tick_type`.
    This parameter is used for low-level customization of OneTick graph nodes and is rarely needed.
  * **start** (``datetime.datetime``, ``otp.datetime``) – Custom start time of the query.
    By default the start time used by ``otp.run`` will be inherited.
  * **end** (``datetime.datetime``, ``otp.datetime``) – Custom end time of the query.
    By default the start time used by ``otp.run`` will be inherited.
  * **date** (``datetime.date``) – Alternative way of setting instead of `start`/`end` times.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.

#### NOTE
Additional fields that can be added to Symbols will be converted to symbol parameters

##### Examples

This class can be used to get a list of all symbols in the database:

```
>>> data = otp.Symbols('US_COMP_SAMPLE', date=otp.dt(2024, 2, 1))  
>>> otp.run(data)                                                  
          Time  SYMBOL_NAME
0   2024-02-01            A
1   2024-02-01          AAL
2   2024-02-01         AAPL
3   2024-02-01         ABBV
4   2024-02-01         ABNB
..         ...          ...
496 2024-02-01          XYL
497 2024-02-01          YUM
498 2024-02-01          ZBH
499 2024-02-01         ZBRA
500 2024-02-01          ZTS
```

By default database name and time interval will be inherited from ``otp.run``:

```
>>> data = otp.Symbols()                                                
>>> otp.run(data, symbols='US_COMP_SAMPLE::', date=otp.dt(2024, 2, 1))  
          Time  SYMBOL_NAME
0   2024-02-01            A
1   2024-02-01          AAL
2   2024-02-01         AAPL
..         ...          ...
```

Parameter `keep_db` can be used to show database name in the output.
It is useful when querying symbols for many databases:

```
>>> data = otp.Symbols(keep_db=True)                                        
>>> data = data.first(2)                                                    
>>> data = otp.merge([data], symbols=['US_COMP_SAMPLE::', 'CME_SAMPLE::'])  
>>> otp.run(data, date=otp.dt(2024, 2, 1))                                  
        Time          SYMBOL_NAME
0 2024-02-01    US_COMP_SAMPLE::A
1 2024-02-01  US_COMP_SAMPLE::AAL
2 2024-02-01  CME_SAMPLE::CL\F25
3 2024-02-01  CME_SAMPLE::CL\F26
..       ...                  ...
```

By default symbols for all tick types are returned.
You can set parameter `show_tick_type` to print the tick type for each symbol:

```
>>> data = otp.Symbols('US_COMP_SAMPLE', show_tick_type=True)  
>>> otp.run(data, date=otp.dt(2024, 2, 1))                     
           Time  SYMBOL_NAME  TICK_TYPE
0    2024-02-01            A        DAY
1    2024-02-01            A       LULD
2    2024-02-01            A       NBBO
3    2024-02-01            A        QTE
4    2024-02-01            A       STAT
..          ...          ...        ...
```

Parameter `for_tick_type` can be used to specify a single tick type for which to return symbols:

```
>>> data = otp.Symbols('US_COMP_SAMPLE', show_tick_type=True, for_tick_type='TRD')  
>>> otp.run(data, date=otp.dt(2024, 2, 1))                                          
          Time  SYMBOL_NAME  TICK_TYPE
0   2024-02-01            A        TRD
1   2024-02-01          AAL        TRD
2   2024-02-01         AAPL        TRD
3   2024-02-01         ABBV        TRD
4   2024-02-01         ABNB        TRD
..         ...          ...        ...
```

Parameter `pattern` can be used to specify the pattern to filter symbol names:

```
>>> data = otp.Symbols('US_COMP_SAMPLE', show_tick_type=True, for_tick_type='TRD',
...                    pattern='AAP_')      
>>> otp.run(data, date=otp.dt(2024, 2, 1))  
        Time SYMBOL_NAME TICK_TYPE
0 2024-02-01        AAPL       TRD
```

Parameter `discard_on_match` can be used to use `pattern` to filter out symbols instead:

```
>>> data = otp.Symbols('US_COMP_SAMPLE', show_tick_type=True, for_tick_type='TRD',
...                    pattern='AAP_', discard_on_match=True)  
>>> otp.run(data, date=otp.dt(2024, 2, 1))                     
          Time  SYMBOL_NAME  TICK_TYPE
0   2024-02-01            A        TRD
1   2024-02-01          AAL        TRD
2   2024-02-01         ABBV        TRD
3   2024-02-01         ABNB        TRD
4   2024-02-01          ABT        TRD
..         ...          ...        ...
```

`otp.Symbols` object can be used to specify symbols for the main query:

```
>>> symbols = otp.Symbols('US_COMP_SAMPLE')                           
>>> symbols = symbols.first(3)                                        
>>> data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')          
>>> result = otp.run(data, symbols=symbols, date=otp.dt(2024, 2, 1))  
>>> result['AAPL'][['Time', 'PRICE', 'SIZE']]                         
                                Time   PRICE  SIZE
0      2024-02-01 04:00:00.008283417  186.50     6
1      2024-02-01 04:00:00.008290927  185.59     1
2      2024-02-01 04:00:00.008291153  185.49   107
3      2024-02-01 04:00:00.010381671  185.49     1
4      2024-02-01 04:00:00.011224206  185.50     2
..                               ...     ...   ...
```

```
>>> result['AAL'][['Time', 'PRICE', 'SIZE']]                          
                                Time  PRICE  SIZE
0      2024-02-01 04:00:00.097381367  14.33     1
1      2024-02-01 04:00:00.138908789  14.37     1
2      2024-02-01 04:00:00.726613365  14.36    10
3      2024-02-01 04:00:02.195702506  14.36    73
4      2024-02-01 04:01:55.268302813  14.39     1
..                               ...    ...   ...
```

Additional fields of the `otp.Symbols` can be used in the main query as symbol parameters:

```
>>> symbols = otp.Symbols('US_COMP_SAMPLE', show_tick_type=True, for_tick_type='TRD')  
>>> symbols['PARAM'] = symbols['SYMBOL_NAME'] + '__' + symbols['TICK_TYPE']            
>>> data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')                           
>>> data = data.first(1)                                                               
>>> data['S_PARAM'] = data.Symbol['PARAM', str]                                        
>>> data = otp.merge([data], symbols=symbols)                                          
>>> data = data[['PRICE', 'SIZE', 'S_PARAM']]                                          
>>> otp.run(data, date=otp.dt(2024, 2, 1))                                             
                             Time    PRICE  SIZE    S_PARAM
0   2024-02-01 04:00:00.001974784  193.800     4   HSY__TRD
1   2024-02-01 04:00:00.003547904   57.810    18   OXY__TRD
2   2024-02-01 04:00:00.006354688   42.810    30   DVN__TRD
3   2024-02-01 04:00:00.007310080  165.890     9   WMT__TRD
4   2024-02-01 04:00:00.007833957   43.170    22  INTC__TRD
..                            ...      ...   ...        ...
```

Use parameter `symbology` to specify different symbology to translate to.
You can also use parameter `show_original_symbols` to print original symbols.
Note that some symbols may not have a translation in target symbology, so their names will be empty:

```
>>> data = otp.Symbols('US_COMP_SAMPLE', for_tick_type='TRD',
...                    symbology='FGV', show_original_symbols=True)                   
>>> otp.run(data, start=otp.dt(2024, 2, 1, 9, 30), end=otp.dt(2024, 2, 1, 9, 30, 1))  
                   Time   SYMBOL_NAME ORIGINAL_SYMBOL_NAME
0   2024-02-01 09:30:00  BBG000C2V3D6                    A
1   2024-02-01 09:30:00  BBG005P7Q881                  AAL
2   2024-02-01 09:30:00  BBG000B9XRY4                 AAPL
3   2024-02-01 09:30:00  BBG0025Y4RY4                 ABBV
4   2024-02-01 09:30:00  BBG001Y2XS07                 ABNB
..                  ...           ...                  ...
496 2024-02-01 09:30:00  BBG001D8R5D0                  XYL
497 2024-02-01 09:30:00  BBG000BH3GZ2                  YUM
498 2024-02-01 09:30:00  BBG000BKPL53                  ZBH
499 2024-02-01 09:30:00  BBG000CC7LQ7                 ZBRA
500 2024-02-01 09:30:00  BBG0039320N9                  ZTS
```

**Escaping special characters in the pattern**

When using `pattern` with special character `\`,
be aware that in python strings `\` is a special character too
and need to be escaped as well:

```
>>> print('back\\slash')
back\slash
```

So, firstly, symbol name `NQ\M23` with SQL LIKE syntax should be matched with pattern `NQ\\M23`:

```
>>> print('NQ\\M23')
NQ\M23
```

Secondly, in python string `"NQ\\M23"` should be escaped as `"NQ\\\\M23"`:

```
>>> print('NQ\\\\M23')
NQ\\M23
```

Escaping character `\` in python can be avoided with python “raw” strings syntax:

```
>>> print(r'NQ\\M23')
NQ\\M23
```

Using raw strings is recommended for readability.

##### SEE ALSO
`Symbols guide`

**FIND_DB_SYMBOLS** OneTick event processor

