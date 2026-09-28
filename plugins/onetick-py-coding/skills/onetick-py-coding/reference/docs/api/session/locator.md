# otp.session.Locator

### ``class Locator(path=None, clean_up=utils.default, copy=True, empty=False, session_ref=None)``

Class representing OneTick database locator.
Locator is the file that describes database name, location and other options.

* **Parameters:**
  * **path** (*str*) – A path to custom locator file. Default is None, that means to generate a temporary locator.
  * **clean_up** (*bool*) – 

    If True, then temporary locator will be removed when Locator object will be destroyed. It is
    helpful for debug purpose.

    By default,
    ``otp.config.clean_up_tmp_files`` is used.
  * **copy** (*bool*) – If True, then the passed custom locator by the `path` parameter will be copied firstly before
    usage. It might be used when you want to work with a custom locator, but don’t want to change
    the original file; in that case a custom locator will be copied into a temporary locator and
    every request for modification will be executed for that temporary locator. Default is True.
  * **empty** (*bool*) – If True, then a temporary locator will have no databases, otherwise it will have default
    otp.config.default_db and COMMON databases. Default is False.
