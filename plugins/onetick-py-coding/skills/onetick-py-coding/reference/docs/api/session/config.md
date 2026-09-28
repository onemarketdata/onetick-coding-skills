# otp.session.Config

### ``class Config(config=None, locator=None, acl=None, otq_path=None, csv_path=None, clean_up=utils.default, copy=True, session_ref=None, license=None, variables=None)``

* **Parameters:**
  * **config** (*path* *or* *Config*) – Allows to specify a custom config. None is to use temporary generated config. Default is None.
  * **locator** (*Locator* *or* *dict* **[*str* *,* *Locator* *]*) – 

    Allows to specify a custom locator file. None is to use temporary generated locator.

    If **dict** passed, adds locators passed as values to corresponding contexts from keys.
    For default context pass locator by **DEFAULT** dictionary key.

    Default is None.
  * **acl** (*ACL*) – Allows to specify a custom acl file. None is to use temporary generated acl. Default is None.
  * **otq_path** (*list* *of* *paths to lookup queries*) – OTQ_PATH parameter in the OneTick config file. Default is None, that is equal to the empty list.
  * **csv_path** (*list* *of* *paths to lookup csv files*) – CSV_PATH parameter in the OneTick config file. Default is None, that is equal to the empty list.
  * **clean_up** (*bool*) – 

    If True, then temporary config file will be removed when the Config instance will be destroyed.
    It is helpful for debug purpose.

    By default,
    ``otp.config.clean_up_tmp_files`` is used.
  * **copy** (*bool*) – If True, then the passed custom config file will be copied firstly before any usage with it.
    It might be used when you want to work with a custom config file, but don’t want to change to
    change the original file; in that case a custom config will be copied into a temporary config
    file and every request for modification will be executed for that temporary config. Default
    is True.
  * **license** (*instance from the onetick.py.license module*) – License to use. If it is not set, then onetick.py.license.Default is used.
  * **variables** (*dict*) – Other values to pass to config.
