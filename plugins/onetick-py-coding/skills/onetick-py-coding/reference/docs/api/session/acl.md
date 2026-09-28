# otp.session.ACL

### ``class ACL(path=None, clean_up=utils.default, copy=True, session_ref=None)``

Class representing OneTick database access list file.
ACL is the file that describes the list of the users
that are allowed to access the database and what permissions do they have.

* **Parameters:**
  * **path** (*str*) – A path to custom acl file. Default is None, that means to generate a temporary acl file.
  * **clean_up** (*bool*) – 

    If True, then temporary acl file will be removed when ACL object will be destroyed. It is
    helpful for debug purpose.

    By default,
    ``otp.config.clean_up_tmp_files`` is used.
  * **copy** (*bool*) – If True, then the passed custom acl file by the `path` parameter will be copied first before
    usage. It might be used when you want to work with a custom acl file, but don’t want to change
    the original file; in that case a custom acl file will be copied into a temporary file and
    every request for modification will be executed for that temporary file. Default is True.
