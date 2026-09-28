# otp.derived_databases

### ``derived_databases(context=utils.default, start=None, end=None, selection_criteria='all', db=None, db_discovery_scope='query_host_only', as_table=False, query_properties=None)``

Gets available derived databases.

* **Parameters:**
  * **context** (*str* *,* *optional*) – Context to run the query.
    If not set then default ``context`` is used.
    See `guide about switching contexts` for examples.
  * **start** (``otp.datetime``, optional) – 

    If both `start` and `end` are set, then listing databases in this range only.
    Otherwise list databases from all configured time ranges for databases.

    If `db` is set, then
    ``otp.config.default_start_time``
    is used by default.
  * **end** (``otp.datetime``, optional) – 

    If both `start` and `end` are set, then listing databases in this range only.
    Otherwise list databases from all configured time ranges for databases.

    If `db` is set, then
    ``otp.config.default_end_time`` is used by default.
  * **selection_criteria** (*str*) – Possible values: *all*, *derived_from_current_db*, *direct_children_of_current_db*.
  * **db** (*str* *,* *optional*) – Specifies database name if `selection_criteria` is set to
    *derived_from_current_db* or *direct_children_of_current_db*.
    Must be set in this case.
    Also specifies symbol name to use when running the query, otherwise
    ``otp.config.default_db`` is used.
  * **db_discovery_scope** (*str*) – When *query_host_and_all_reachable_hosts* is specified,
    an attempt will be performed to get derived databases from all reachable hosts.
    When *query_host_only* is specified,
    only derived databases from the host on which the query is performed will be returned.
  * **as_table** (*bool*) – If False (default), this function returns a dictionary of database names and database objects.
    If True, returns a `pandas.DataFrame` table where each row contains the info for each database.
  * **query_properties** (*dict* *,* *optional*) – Query properties passed to ``otp.run``,
    such as ONE_TO_MANY_POLICY, ALLOW_GRAPH_REUSE, etc.
* **Returns:**
  * Dict where keys are database names and values are ``DB`` objects
  * or `pandas.DataFrame` object depending on `as_table` parameter.
* **Return type:**
  `dict``str`, [*DB*]

##### SEE ALSO
**SHOW_DERIVED_DB_LIST** OneTick event processor
