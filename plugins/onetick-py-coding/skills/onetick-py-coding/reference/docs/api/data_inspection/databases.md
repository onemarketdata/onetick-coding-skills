# otp.databases

### ``databases(context=utils.default, derived=False, readable_only=False, fetch_description=None, as_table=False, query_properties=None)``

Gets all available databases in the `context`.

* **Parameters:**
  * **context** (*str* *,* *optional*) – Context to run the query.
    If not set then default ``context`` is used.
    See `guide about switching contexts` for examples.
  * **derived** (*bool* *,* *dict*) – If False (default) then derived databases are not returned.
    Otherwise derived databases names are added to the result after the non-derived databases.
    If set to dict then its items used as parameters to ``derived_databases()``.
    If set to True then default parameters for ``derived_databases()`` are used.
  * **readable_only** (*bool*) – 

    If set to True, then return only the databases with read-access for the current user.
    If set to False (default), return all databases visible from the current process.

    #### WARNING
    Setting this flag may introduce significant delay for query execution
    in some OneTick configurations and environments,
    because it has to check all accessible remote servers.
  * **fetch_description** (*bool*) – If set to True, retrieves descriptions for databases and puts them into `description` property of
    ``DB`` objects in a returned dict.
  * **as_table** (*bool*) – If False (default), this function returns a dictionary of database names and database objects.
    If True, returns a `pandas.DataFrame` table where each row contains the info for each database.
  * **query_properties** (*dict* *,* *optional*) – Query properties passed to ``otp.run``,
    such as ONE_TO_MANY_POLICY, ALLOW_GRAPH_REUSE, etc.
* **Returns:**
  * Dict where keys are database names and values are ``DB`` objects
  * or `pandas.DataFrame` object depending on `as_table` parameter.
* **Return type:**
  `dict``str`, [*DB*] | *DataFrame*

##### Examples

Get the dictionary of database names and objects:

```
>>> otp.databases()  
{'ABU_DHABI': <onetick.py.db._inspection.DB at 0x7f9413a5e8e0>,
 'ABU_DHABI_BARS': <onetick.py.db._inspection.DB at 0x7f9413a5ef40>,
 'ABU_DHABI_DAILY': <onetick.py.db._inspection.DB at 0x7f9413a5eac0>,
 'ALPHA': <onetick.py.db._inspection.DB at 0x7f9413a5e940>,
 'ALPHA_X': <onetick.py.db._inspection.DB at 0x7f9413a5e490>,
 ...
}
```

Get a table with database info:

```
>>> otp.databases(as_table=True)  
           Time            DB_NAME  READ_ACCESS  WRITE_ACCESS         ...
0    2003-01-01          ABU_DHABI            1             0         ...
1    2003-01-01     ABU_DHABI_BARS            1             1         ...
2    2003-01-01    ABU_DHABI_DAILY            1             1         ...
3    2003-01-01              ALPHA            1             1         ...
4    2003-01-01            ALPHA_X            1             1         ...
...         ...                ...          ...           ...         ...
```

##### SEE ALSO
**SHOW_DB_LIST** OneTick event processor

**ACCESS_INFO** OneTick event processor

``derived_databases()``

