# otp.RefData

### ``class RefData(ref_data_type=None, symbol=utils.adaptive, db=utils.adaptive_to_default, start=utils.adaptive, end=utils.adaptive, query_parameters=None, **kwargs)``

Bases: ``Source``

Shows reference data for the specified security and reference data type.

It can be used to view corporation actions,
symbol name changes,
primary exchange info and symbology mapping for a securities,
as well as the list of symbologies,
names of custom adjustment types for corporate actions present in a reference database
as well as names of continuous contracts in database symbology.

* **Parameters:**
  * **ref_data_type** (*str*) – 

    Type of reference data to be queried. Possible values are:
    * corp_actions
    * symbol_name_history
    * primary_exchange
    * symbol_calendar
    * symbol_currency
    * symbology_mapping
    * symbology_list
    * custom_adjustment_type_list
    * all_calendars
    * all_continuous_contract_names
  * **symbol** (str, list of str, ``Source``, ``query``, ``eval query``) – Symbol(s) from which data should be taken.
  * **db** (*str*) – Name of the database.
  * **start** (``otp.datetime``) – Start time for tick generation. By default the start time of the query will be used.
  * **end** (``otp.datetime``) – End time for tick generation. By default the end time of the query will be used.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.

##### Examples

Show calendars for a database US_COMP_SAMPLE:

```
>>> src = otp.RefData('all_calendars')  
>>> otp.run(src, symbols='US_COMP_SAMPLE::AAPL',
...         date=otp.dt(2024, 2, 1), symbol_date=otp.dt(2024, 2, 1), timezone='EST5EDT')  
         Time END_DATETIME       CALENDAR_NAME SESSION_NAME SESSION_FLAGS DAY_PATTERN                     START_HHMMSS  END_HHMMSS          TIMEZONE  PRIORITY                    DESCRIPTION
0  2024-02-01   2024-03-29  BBG_EQUITY_EXCH_US     DAY_TYPE             R   0.0.12345                                0      240000  America/New_York         0                    @US_DEFAULT
1  2024-02-01   2024-03-29  BBG_EQUITY_EXCH_US   PRE_MARKET             b   0.0.12345                            40000       93000  America/New_York         0                    @US_DEFAULT
2  2024-02-01   2024-03-29  BBG_EQUITY_EXCH_US       MARKET             r   0.0.12345                            93000      160000  America/New_York         0                    @US_DEFAULT
3  2024-02-01   2024-03-29  BBG_EQUITY_EXCH_US  POST_MARKET             a   0.0.12345                           160000      200000  America/New_York         0                    @US_DEFAULT
4  2024-02-01   2024-03-29  BBG_EQUITY_EXCH_US      HOLIDAY             H       1.3.1                                0      240000  America/New_York         1  MARTIN_LUTHER_KING@US_DEFAULT
..        ...          ...                 ...          ...           ...         ...                              ...         ...               ...       ...                            ...
85 2024-02-01   2024-03-29     CLOUD_DB_US_OTC      HOLIDAY             H       1.3.1                                0      240000  America/New_York         1  MARTIN_LUTHER_KING@US_DEFAULT
86 2024-02-01   2024-03-29     CLOUD_DB_US_OTC      HOLIDAY             H       2.3.1                                0      240000  America/New_York         1      PRESIDENTS_DAY@US_DEFAULT
87 2024-02-01   2024-03-29     CLOUD_DB_US_OTC      HOLIDAY             H       5.6.1                                0      240000  America/New_York         1        MEMORIAL_DAY@US_DEFAULT
88 2024-02-01   2024-03-29     CLOUD_DB_US_OTC      HOLIDAY             H       9.1.1                                0      240000  America/New_York         1           LABOR_DAY@US_DEFAULT
89 2024-02-01   2024-03-29     CLOUD_DB_US_OTC      HOLIDAY             H      11.4.4                                0      240000  America/New_York         1    THANKSGIVING_DAY@US_DEFAULT
```

##### SEE ALSO
**REF_DATA** OneTick event processor
