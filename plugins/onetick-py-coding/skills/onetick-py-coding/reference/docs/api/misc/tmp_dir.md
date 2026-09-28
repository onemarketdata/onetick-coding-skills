# otp.utils.TmpDir

### ``class TmpDir(rel_path='', *, suffix='', clean_up=default, base_dir=default)``

Bases: ``PathLike``, `CleanUpFinalizer`

Class to create a temporary directory.
By default, this directory will be deleted automatically after python process exits.
All files and directories under this one will be deleted too.

Base path where directories are created could be set using the `ONE_TICK_TMP_DIR`.
By default they are created under the `tempfile.gettempdir()` folder.

* **Parameters:**
  * **rel_path** (*str*) – relative path to the temporary directory.
    If empty, then the name will be auto-generated.
  * **suffix** (*str*) – suffix of the name of the temporary directory.
  * **base_dir** (*str*) – relative path of the directory where temporary directory will be created.
    By default, the directory of current ``otp.Session`` is used.
  * **clean_up** (*bool*) – 

    Controls whether this temporary directory will be deleted automatically
    after python process exits.

    By default, the value of current ``otp.Session`` is used.
    If it’s not set, then
    ``otp.config.clean_up_tmp_files`` is used.

##### SEE ALSO
The testing framework has a `--keep-generated` flag that controls clean up for all related instances
`onetick-py-test plugin features`
