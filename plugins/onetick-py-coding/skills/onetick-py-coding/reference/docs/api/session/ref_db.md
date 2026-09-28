# otp.RefDB

### ``class RefDB(name=None, kind='archive', db_properties=None, db_location=None, write=True, clean_up=utils.default, destroy_access=False)``

Bases: ``DB``

Creates reference database object.

* **Parameters:**
  * **name** (*str*) – Database name
  * **clean_up** (*bool* *,* *optional*) – Flag that controls temporary database cleanup
  * **db_properties** (``dict``, optional) – Properties of database to add to locator
  * **db_location** (``dict``, optional) – Location of database to add to locator. Reference database must have a single location,
    pointing to a continuous archive database.
  * **write** (*bool* *,* *optional*) – Flag that controls access to write to database
  * **destroy_access** (*bool* *,* *optional*) – Flag that controls access to destroy to database

##### Examples

```
>>> properties = {'symbology': 'TICKER'}
>>> location = {'archive_duration': 'continuous'}
>>> ref_db = otp.RefDB('REF_DATA_MYDB', db_properties=properties, db_location=location)
>>> session.use(ref_db)
>>> data = 'A||20100102000000|20100103000000|B||20100103000000|20100104000000|'
>>> out, err = ref_db.put([otp.RefDB.SymbolNameHistory(data, 'TICKER')])
>>> b'Total ticks 8' in err and b'Total symbols 6' in err
True
>>> properties = {'ref_data_db': ref_db.name, 'symbology': 'TICKER'}
>>> db = otp.DB('MYDB', db_properties=properties)
>>> session.use(db)
>>> data = otp.Ticks(X=['hello'], start=otp.dt(2010, 1, 2), end=otp.dt(2010, 1, 3))
>>> data = otp.run(data.write(db.name, 'A', 'MSG', date=otp.dt(2010, 1, 2)))
>>> data = otp.Ticks(X=['world!'], start=otp.dt(2010, 1, 3), end=otp.dt(2010, 1, 4))
>>> data = otp.run(data.write(db.name, 'B', 'MSG', date=otp.dt(2010, 1, 3)))
>>> data = otp.DataSource(db.name, tick_type='MSG')
>>> s_dt, e_dt, symbol_date = otp.dt(2010, 1, 1), otp.dt(2010, 1, 4), otp.dt(2010, 1, 2)
>>> otp.run(data, symbols='A', start=s_dt, end=e_dt, symbol_date=symbol_date)
        Time       X
0 2010-01-02   hello
1 2010-01-03  world!
```

#### ``RefDB.put(src, tickdb_symbology=None, delta_mode=False, full_integrity_check=False, load_by_sections=True)``

Loads data in database with reference_data_loader.exe.
If db properties contain SUPPORT_DELTAS=YES, delta_mode set to True, and proper delta file is used
then data is loaded in incremental mode (in other words, replace or modification mode).
If the above conditions are not met, reference database content is entirely rewritten with the new data.

* **Parameters:**
  * **src** (*str* *,* *list* *of* *str* *or* *otp.RefDB.Section*) – Path to data file, or list of data per section in specified format
  * **tickdb_symbology** (*list* *of* *str* *,* *optional*) – All symbologies for which the reference data needs to be generated
  * **delta_mode** (*bool* *,* *default is False*) – If set to True loader will perform incremental load. Cannot be used if `tickdb_symbology` is specified
  * **full_integrity_check** (*bool* *,* *default is False*) – If set to True loader checks all mappings to symbologies with symbol name history section
    and gives warning if mapped securities do not have symbol name history
  * **load_by_sections** (*bool* *,* *default is True*) – If set to True loader will perform input data file splitting by data types and symbologies
    to load each part separately instead loading the entire file at once

### ``class Section(name, data, attrs=None)``

Bases: ``object``

Specification of a reference database section. Section content can be specified as a string or source.
The format of string and output columns of source must correspond with the section documentation.

* **Parameters:**
  * **name** (*str*) – Section name
  * **data** (str or `otp.Source`) – Content of the section
  * **attrs** (``dict``, optional) – Attributes of the section

##### Examples

Data provided as a string:

```
>>> data = 'SYM1|20100101093000|20100101110000' + os.linesep
>>> data += 'SYM2|20100101110000|20100103140000'
>>> section = otp.RefDB.Section('SECTION_NAME', data, {'ATTR1': 'VAL1', 'ATTR2': 'VAL2'})
>>> print(section)
<SECTION_NAME ATTR1="VAL1" ATTR2="VAL2">
SYM1|20100101093000|20100101110000
SYM2|20100101110000|20100103140000
</SECTION_NAME>
```

Data provided as a `otp.Source`:

```
>>> data = dict()
>>> data['SYMBOL_NAME'] = ['SYM1', 'SYM2']
>>> data['START_DATETIME'] = [otp.dt(2010, 1, 1, 9, 30, tz='EST5EDT'), otp.dt(2010, 1, 1, 11, tz='EST5EDT')]
>>> data['END_DATETIME'] = [otp.dt(2010, 1, 1, 11, tz='EST5EDT'), otp.dt(2010, 1, 3, 14, tz='EST5EDT')]
>>> ticks = otp.Ticks(**data, offset=[0] * 2, db='LOCAL')
>>> section = otp.RefDB.Section('SECTION_NAME', ticks, {'ATTR1': 'VAL1', 'ATTR2': 'VAL2'})
>>> print(section) 
<SECTION_NAME ATTR1="VAL1" ATTR2="VAL2" OTQ_QUERY=...>
</SECTION_NAME>
```

where OTQ_QUERY is path to `otp.Source`, dumped to disk as temporary .otq file.

### ``class SymbolNameHistory(data, symbology)``

Bases: ``Section``

Describes symbol changes for the same security. The continuity can be expressed in terms of any symbol type
and can be specified on the security level or the security+exchange level (more explicit).

##### Examples

```
>>> data = 'CORE_A||20100101093000|20100101110000|CORE_B||20100101110000|20100103140000|'
>>> section = otp.RefDB.SymbolNameHistory(data, symbology='CORE')
>>> print(section)
<SYMBOL_NAME_HISTORY SYMBOLOGY="CORE">
CORE_A||20100101093000|20100101110000|CORE_B||20100101110000|20100103140000|
</SYMBOL_NAME_HISTORY>
```

Equivalent `otp.Source`:

```
>>> data = dict()
>>> data['SYMBOL_NAME'] = ['CORE_A'] * 2
>>> data['SYMBOL_NAME_IN_HISTORY'] = ['CORE_A', 'CORE_B']
>>> data['SYMBOL_START_DATETIME'] = [otp.dt(2010, 1, 2, tz='EST5EDT')] * 2
>>> data['SYMBOL_END_DATETIME'] = [otp.dt(2010, 1, 5, tz='EST5EDT')] * 2
>>> data['START_DATETIME'] = [otp.dt(2010, 1, 2, tz='EST5EDT'), otp.dt(2010, 1, 3, tz='EST5EDT')]
>>> data['END_DATETIME'] = [otp.dt(2010, 1, 3, tz='EST5EDT'), otp.dt(2010, 1, 4, tz='EST5EDT')]
>>> ticks = otp.Ticks(**data, offset=[0] * 2, db='LOCAL')
>>> section = otp.RefDB.SymbolNameHistory(ticks, symbology='CORE')
>>> print(section) 
<SYMBOL_NAME_HISTORY SYMBOLOGY="CORE" OTQ_QUERY=...>
</SYMBOL_NAME_HISTORY>
```

* **Parameters:**
  * **data** (*str* *|* *otp.Source*)
  * **symbology** (*str*)

### ``class SymbologyMapping(data, source_symbology, dest_symbology)``

Bases: ``Section``

Describes a history of mapping of symbols of one symbology to the symbols of another symbology.

##### Examples

```
>>> data = 'A||20100101093000|20100101110000|CORE_A|' + os.linesep
>>> data += 'B||20100101110000|20100103140000|CORE_B|'
>>> section = otp.RefDB.SymbologyMapping(data, source_symbology='TICKER', dest_symbology='CORE')
>>> print(section)
<SYMBOLOGY_MAPPING SOURCE_SYMBOLOGY="TICKER" DEST_SYMBOLOGY="CORE">
A||20100101093000|20100101110000|CORE_A|
B||20100101110000|20100103140000|CORE_B|
</SYMBOLOGY_MAPPING>
```

Equivalent `otp.Source`:

```
>>> data = dict()
>>> data['SYMBOL_NAME'] = ['A', 'B']
>>> data['MAPPED_SYMBOL_NAME'] = ['CORE_A', 'CORE_B']
>>> data['START_DATETIME'] = [otp.dt(2010, 1, 2, tz='EST5EDT'), otp.dt(2010, 1, 3, tz='EST5EDT')]
>>> data['END_DATETIME'] = [otp.dt(2010, 1, 3, tz='EST5EDT'), otp.dt(2010, 1, 4, tz='EST5EDT')]
>>> ticks = otp.Ticks(**data, offset=[0] * 2, db='LOCAL')
>>> section = otp.RefDB.SymbologyMapping(ticks, source_symbology='TICKER', dest_symbology='CORE')
>>> print(section) 
<SYMBOLOGY_MAPPING SOURCE_SYMBOLOGY="TICKER" DEST_SYMBOLOGY="CORE" OTQ_QUERY=...>
</SYMBOLOGY_MAPPING>
```

* **Parameters:**
  * **data** (*str* *|* *otp.Source*)
  * **source_symbology** (*str*)
  * **dest_symbology** (*str*)

### ``class CorpActions(data, symbology)``

Bases: ``Section``

Describes corporate actions. Used by OneTick to compute prices adjusted for various types of
corporate actions. Supports both built-in and custom (user-defined) types of corporate actions.

##### Examples

```
>>> data = 'CORE_C||20100103180000|0.25|0.0|SPLIT'
>>> section = otp.RefDB.CorpActions(data, symbology='CORE')
>>> print(section)
<CORP_ACTIONS SYMBOLOGY="CORE">
CORE_C||20100103180000|0.25|0.0|SPLIT
</CORP_ACTIONS>
```

Equivalent `otp.Source`:

```
>>> data = dict()
>>> data['SYMBOL_NAME'] = ['CORE_C']
>>> data['EFFECTIVE_DATETIME'] = [otp.dt(2010, 1, 3, 18, tz='EST5EDT')]
>>> data['MULTIPLICATIVE_ADJUSTMENT'] = [0.25]
>>> data['ADDITIVE_ADJUSTMENT'] = [0.0]
>>> data['ADJUSTMENT_TYPE_NAME'] = ['SPLIT']
>>> ticks = otp.Ticks(**data, offset=[0], db='LOCAL')
>>> section = otp.RefDB.CorpActions(ticks, symbology='CORE')
>>> print(section) 
<CORP_ACTIONS SYMBOLOGY="CORE" OTQ_QUERY=...>
</CORP_ACTIONS>
```

* **Parameters:**
  * **data** (*str* *|* *otp.Source*)
  * **symbology** (*str*)

### ``class ContinuousContracts(data, symbology)``

Bases: ``Section``

Describes continuous contracts. Continuity is expressed in terms of stitched history
of real contracts and rollover adjustments in between them and can be specified
on the continuous contract level or continuous contract+exchange level (more explicit).

##### Examples

```
>>> data = 'CC||CORE_A||20100101093000|20100101110000|0.5|0|CORE_B||20100101110000|20100103140000'
>>> section = otp.RefDB.ContinuousContracts(data, symbology='CORE')
>>> print(section)
<CONTINUOUS_CONTRACTS SYMBOLOGY="CORE">
CC||CORE_A||20100101093000|20100101110000|0.5|0|CORE_B||20100101110000|20100103140000
</CONTINUOUS_CONTRACTS>
```

Equivalent `otp.Source`:

```
>>> data = dict()
>>> data['CONTINUOUS_CONTRACT_NAME'] = ['CC'] * 2
>>> data['SYMBOL_NAME'] = ['CORE_A', 'CORE_B']
>>> data['START_DATETIME'] = [otp.dt(2010, 1, 2, tz='EST5EDT'), otp.dt(2010, 1, 3, tz='EST5EDT')]
>>> data['END_DATETIME'] = [otp.dt(2010, 1, 3, tz='EST5EDT'), otp.dt(2010, 1, 4, tz='EST5EDT')]
>>> data['MULTIPLICATIVE_ADJUSTMENT'] = [0.5, None]
>>> data['ADDITIVE_ADJUSTMENT'] = [3, None]
>>> ticks = otp.Ticks(**data, offset=[0] * 2, db='LOCAL')
>>> section = otp.RefDB.ContinuousContracts(ticks, symbology='CORE')
>>> print(section) 
<CONTINUOUS_CONTRACTS SYMBOLOGY="CORE" OTQ_QUERY=...>
</CONTINUOUS_CONTRACTS>
```

* **Parameters:**
  * **data** (*str* *|* *otp.Source*)
  * **symbology** (*str*)

### ``class SymbolCurrency(data, symbology)``

Bases: ``Section``

Specifies symbols’ currencies in 3-letter ISO codes for currencies. These are used for currency conversion
(e.g., when calculating portfolio price for a list of securities with different currencies).

##### Examples

```
>>> data = 'CORE_A||20100101093000|20100101110000|USD|1.0' + os.linesep
>>> data += 'CORE_B||20100101110000|20100103140000|RUB|1.8'
>>> section = otp.RefDB.SymbolCurrency(data, symbology='CORE')
>>> print(section)
<SYMBOL_CURRENCY SYMBOLOGY="CORE">
CORE_A||20100101093000|20100101110000|USD|1.0
CORE_B||20100101110000|20100103140000|RUB|1.8
</SYMBOL_CURRENCY>
```

Equivalent `otp.Source`:

```
>>> data = dict()
>>> data['SYMBOL_NAME'] = ['CORE_A', 'CORE_B',]
>>> data['CURRENCY'] = ['USD', 'RUB']
>>> data['MULTIPLIER'] = [1., 1.8]
>>> data['START_DATETIME'] = [otp.dt(2010, 1, 1, 9, 30, tz='EST5EDT'), otp.dt(2010, 1, 1, 11, tz='EST5EDT')]
>>> data['END_DATETIME'] = [otp.dt(2010, 1, 1, 11, tz='EST5EDT'), otp.dt(2010, 1, 3, 14, tz='EST5EDT')]
>>> ticks = otp.Ticks(**data, offset=[0] * 2, db='LOCAL')
>>> section = otp.RefDB.SymbolCurrency(ticks, symbology='CORE')
>>> print(section) 
<SYMBOL_CURRENCY SYMBOLOGY="CORE" OTQ_QUERY=...>
</SYMBOL_CURRENCY>
```

* **Parameters:**
  * **data** (*str* *|* *otp.Source*)
  * **symbology** (*str*)

### ``class Calendar(data)``

Bases: ``Section``

Specifies a named calendar. Needed to analyze tick data during specific market time intervals (i.e., during
normal trading hours). Can either be used directly in queries as described below, or referred to
from the SYMBOL_CALENDAR and EXCH_CALENDAR sections.

##### Examples

```
>>> data = 'CAL1|20100101093000|20100101110000|Regular|R|0.0.12345|093000|160000|GMT|1|DESCRIPTION1'
>>> data += os.linesep
>>> data += 'CAL2|20100101110000|20100103140000|Holiday|F|0.0.12345|094000|170000|GMT|0|DESCRIPTION2'
>>> section = otp.RefDB.Calendar(data)
>>> print(section)
<CALENDAR >
CAL1|20100101093000|20100101110000|Regular|R|0.0.12345|093000|160000|GMT|1|DESCRIPTION1
CAL2|20100101110000|20100103140000|Holiday|F|0.0.12345|094000|170000|GMT|0|DESCRIPTION2
</CALENDAR>
```

Equivalent `otp.Source`:

```
>>> data = dict()
>>> data['CALENDAR_NAME'] = ['CAL1', 'CAL2']
>>> data['START_DATETIME'] = [otp.dt(2010, 1, 1, 9, 30, tz='EST5EDT'), otp.dt(2010, 1, 1, 11, tz='EST5EDT')]
>>> data['END_DATETIME'] = [otp.dt(2010, 1, 1, 11, tz='EST5EDT'), otp.dt(2010, 1, 3, 14, tz='EST5EDT')]
>>> data['SESSION_NAME'] = ['Regular', 'Holiday']
>>> data['SESSION_FLAGS'] = ['R', 'H']
>>> data['DAY_PATTERN'] = ['0.0.12345', '0.0.12345']
>>> data['START_HHMMSS'] = ['093000', '094000']
>>> data['END_HHMMSS'] = ['160000', '170000']
>>> data['TIMEZONE'] = ['GMT', 'GMT']
>>> data['PRIORITY'] = [1, 0]
>>> data['DESCRIPTION'] = ['DESCRIPTION1', 'DESCRIPTION2']
>>> ticks = otp.Ticks(**data, offset=[0] * 2, db='LOCAL')
>>> section = otp.RefDB.Calendar(ticks)
>>> print(section) 
<CALENDAR  OTQ_QUERY=...>
</CALENDAR>
```

* **Parameters:**
  **data** (*str* *|* *otp.Source*)

### ``class SymbolCalendar(data, symbology)``

Bases: ``Section``

Specifies a calendar for a symbol. Needed to analyze tick data during specific market time intervals
(i.e., during normal trading hours). Can either be specified directly or refer to a named calendar by its name
(see the CALENDAR section).

##### Examples

Symbol calendar section, referring to named calendar section:

```
>>> data = 'CORE_A|20100101093000|20100101110000|CAL1' + os.linesep
>>> data += 'CORE_B|20100101110000|20100103140000|CAL2'
>>> section = otp.RefDB.SymbolCalendar(data, symbology='CORE')
>>> print(section)
<SYMBOL_CALENDAR SYMBOLOGY="CORE">
CORE_A|20100101093000|20100101110000|CAL1
CORE_B|20100101110000|20100103140000|CAL2
</SYMBOL_CALENDAR>
```

Equivalent `otp.Source`:

```
>>> data = dict()
>>> data['SYMBOL_NAME'] = ['CORE_A', 'CORE_B']
>>> data['START_DATETIME'] = [otp.dt(2010, 1, 1, 9, 30, tz='EST5EDT'), otp.dt(2010, 1, 1, 11, tz='EST5EDT')]
>>> data['END_DATETIME'] = [otp.dt(2010, 1, 1, 11, tz='EST5EDT'), otp.dt(2010, 1, 3, 14, tz='EST5EDT')]
>>> data['CALENDAR_NAME'] = ['CAL1', 'CAL2']
>>> ticks = otp.Ticks(**data, offset=[0] * 2, db='LOCAL')
>>> section = otp.RefDB.SymbolCalendar(ticks, symbology='CORE')
>>> print(section) 
<SYMBOL_CALENDAR SYMBOLOGY="CORE" OTQ_QUERY=...>
</SYMBOL_CALENDAR>
```

Symbol calendar section without using named calendar section:

```
>>> data = 'CORE_A|20100101093000|20100101110000|Regular|R|0.0.12345|093000|160000|EST5EDT|1|' + os.linesep
>>> data += 'CORE_B|20100101110000|20100103140000|Regular|F|0.0.12345|093000|160000|EST5EDT|1|'
>>> section = otp.RefDB.SymbolCalendar(data, symbology='CORE')
>>> print(section)
<SYMBOL_CALENDAR SYMBOLOGY="CORE">
CORE_A|20100101093000|20100101110000|Regular|R|0.0.12345|093000|160000|EST5EDT|1|
CORE_B|20100101110000|20100103140000|Regular|F|0.0.12345|093000|160000|EST5EDT|1|
</SYMBOL_CALENDAR>
```

Equivalent `otp.Source`:

```
>>> data = dict()
>>> data['SYMBOL_NAME'] = ['CORE_A', 'CORE_B']
>>> data['START_DATETIME'] = [otp.dt(2010, 1, 1, 9, 30, tz='EST5EDT'), otp.dt(2010, 1, 1, 11, tz='EST5EDT')]
>>> data['END_DATETIME'] = [otp.dt(2010, 1, 1, 11, tz='EST5EDT'), otp.dt(2010, 1, 3, 14, tz='EST5EDT')]
>>> data['SESSION_NAME'] = ['Regular', 'Regular']
>>> data['SESSION_FLAGS'] = ['R', 'F']
>>> data['DAY_PATTERN'] = ['0.0.12345', '0.0.12345']
>>> data['START_HHMMSS'] = ['093000', '160000']
>>> data['END_HHMMSS'] = ['CAL1', 'CAL2']
>>> data['TIMEZONE'] = ['EST5EDT', 'EST5EDT']
>>> data['PRIORITY'] = [1, 1]
>>> data['DESCRIPTION'] = ['', '']
>>> ticks = otp.Ticks(**data, offset=[0] * 2, db='LOCAL')
>>> section = otp.RefDB.SymbolCalendar(ticks, symbology='CORE')
>>> print(section) 
<SYMBOL_CALENDAR SYMBOLOGY="CORE" OTQ_QUERY=...>
</SYMBOL_CALENDAR>
```

* **Parameters:**
  * **data** (*str* *|* *otp.Source*)
  * **symbology** (*str*)

### ``class SectionStr(name, data, attrs=None)``

Bases: ``Section``

Specification of a reference database section that can be specified only as a string.
Section content still can be provided as a `otp.Source`, but the `otp.Source` is executed and
result data is used as string in section. It’s up to user to provide `otp.Source` with correct number
and order of columns.

##### Examples

Data provided as a string returns the same result as `otp.RefDB.Section`.

Data provided as a `otp.Source`:

```
>>> data = dict()
>>> data['SYMBOL_NAME'] = ['SYM1', 'SYM2']
>>> data['START_DATETIME'] = [otp.dt(2010, 1, 1, 9, 30, tz='EST5EDT'), otp.dt(2010, 1, 1, 11, tz='EST5EDT')]
>>> data['END_DATETIME'] = [otp.dt(2010, 1, 1, 11, tz='EST5EDT'), otp.dt(2010, 1, 3, 14, tz='EST5EDT')]
>>> ticks = otp.Ticks(**data, offset=[0] * 2, db='LOCAL')
>>> ticks = ticks.table(SYMBOL_NAME=otp.string[128], START_DATETIME=otp.msectime, END_DATETIME=otp.msectime)
>>> section = otp.RefDB.SectionStr('SECTION_NAME', ticks, {'ATTR1': 'VAL1', 'ATTR2': 'VAL2'})
>>> print(section) 
<SECTION_NAME ATTR1="VAL1" ATTR2="VAL2">
SYM1|20100101093000|20100101110000
SYM2|20100101110000|20100103140000
</SECTION_NAME>
```

where OTQ_QUERY is path to `otp.Source`, dumped to disk as temporary .otq file.

* **Parameters:**
  * **name** (*str*)
  * **data** (*str* *|* *otp.Source*)
  * **attrs** (*dict* *|* *None*)

### ``class PrimaryExchange(data, symbology)``

Bases: ``SectionStr``

Specifies symbols’ primary exchanges. Used to extract and analyze tick data for a security on
its primary exchange, without having to explicitly specify the name of the primary exchange.

##### Examples

```
>>> data = 'A||19991118000000|99999999000000|N|'
>>> data += os.linesep
>>> data += 'AA||19991118000000|99999999000000|N|AA.N'
>>> section = otp.RefDB.PrimaryExchange(data, symbology='TICKER')
>>> print(section)
<PRIMARY_EXCHANGE SYMBOLOGY="TICKER">
A||19991118000000|99999999000000|N|
AA||19991118000000|99999999000000|N|AA.N
</PRIMARY_EXCHANGE>
```

Equivalent query should return the same data values in the same order. Column names does not matter.

* **Parameters:**
  * **data** (*str* *|* *otp.Source*)
  * **symbology** (*str*)

### ``class ExchCalendar(data, symbology)``

Bases: ``SectionStr``

Specifies symbols’ primary exchanges. Used to extract and analyze tick data for a security on
its primary exchange, without having to explicitly specify the name of the primary exchange.

##### Examples

```
>>> data = 'NYSE||19600101000000|20501231235959|Regular|R|0.0.12345|093000|160000|EST5EDT|'
>>> data += os.linesep
>>> data += 'NYSE||19600101000000|20501231235959|Half-day|RL|12/31|093000|130000|EST5EDT|'
>>> data += os.linesep
>>> data += 'NYSE||19600101000000|20501231235959|Holiday|H|01/01|000000|240000|EST5EDT|'
>>> section = otp.RefDB.ExchCalendar(data, symbology='MIC')
>>> print(section)
<EXCH_CALENDAR SYMBOLOGY="MIC">
NYSE||19600101000000|20501231235959|Regular|R|0.0.12345|093000|160000|EST5EDT|
NYSE||19600101000000|20501231235959|Half-day|RL|12/31|093000|130000|EST5EDT|
NYSE||19600101000000|20501231235959|Holiday|H|01/01|000000|240000|EST5EDT|
</EXCH_CALENDAR>
```

If a CALENDAR section is used:

```
>>> data = 'LSE||19600101000000|20501231235959|WNY'
>>> section = otp.RefDB.ExchCalendar(data, symbology='MIC')
>>> print(section)
<EXCH_CALENDAR SYMBOLOGY="MIC">
LSE||19600101000000|20501231235959|WNY
</EXCH_CALENDAR>
```

Equivalent query should return the same data values in the same order. Column names does not matter.

* **Parameters:**
  * **data** (*str* *|* *otp.Source*)
  * **symbology** (*str*)

### ``class SymbolExchange(data, symbology, exchange_symbology)``

Bases: ``SectionStr``

Specifies the exchange where a security is traded. Needs to be provided for the symbologies where
the symbol name is unique across all exchanges.

##### Examples

```
>>> data = 'IBM.N|19980825000000|20501231235959|NYSE||'
>>> section = otp.RefDB.SymbolExchange(data, symbology='RIC', exchange_symbology='MIC')
>>> print(section)
<SYMBOL_EXCHANGE SYMBOLOGY="RIC" EXCHANGE_SYMBOLOGY="MIC">
IBM.N|19980825000000|20501231235959|NYSE||
</SYMBOL_EXCHANGE>
```

Equivalent query should return the same data values in the same order. Column names does not matter.

* **Parameters:**
  * **data** (*str* *|* *otp.Source*)
  * **symbology** (*str*)
  * **exchange_symbology** (*str*)
