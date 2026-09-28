# otp.utils.TmpFile

### ``class TmpFile(suffix='', name='', clean_up=default, force=False, base_dir=default)``

Bases: `File`, ``PathLike``, `CleanUpFinalizer`

Class to create a temporary file.
By default, this file will be deleted automatically after python process exits.
Base path where temporary files are created could be set using the `ONE_TICK_TMP_DIR`.
By default they are created under the `tempfile.gettempdir()` folder.

* **Parameters:**
  * **name** (*str*) – name of the temporary file without suffix.
    By default some random name will be generated.
  * **suffix** (*str*) – suffix of the name of the temporary file.
  * **clean_up** (*bool*) – 

    Controls whether this temporary file will be deleted automatically after python process exits.

    By default, the value of current ``otp.Session`` is used.
    If it’s not set, then
    ``otp.config.clean_up_tmp_files`` is used.
  * **force** (*bool*) – Rewrite temporary file if it exists and parameter `name` is set.
  * **base_dir** (*str*) – Absolute path of the directory where temporary file will be created.
    By default, the directory of current ``otp.Session`` is used.

##### SEE ALSO
The testing framework has a `--keep-generated` flag that controls clean up for all related instances
`onetick-py-test plugin features`
