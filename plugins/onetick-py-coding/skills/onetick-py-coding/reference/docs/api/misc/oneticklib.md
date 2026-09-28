# otp.OneTickLib

### ``class OneTickLib(*args, **kwargs)``

Bases: ``object``

Singleton class for `otq.OneTickLib` to initialize it once.

Returns the same object if it was already initialized.

#### ``cleanup()``

Destroy otq.OneTickLib instance and reset singleton class

#### ``set_log_file(log_file)``

Set log file for given instance of OneTickLib

* **Parameters:**
  **log_file** – path to log file

#### ``set_logging_level(lvl)``

Logging level can be specified by using LoggingLevel Enum class

* **Parameters:**
  **lvl** (*LoggingLevel*) – available values are LoggingLevel.MIN, LoggingLevel.LOW, LoggingLevel.MEDIUM or LoggingLevel.MAX
* **Returns:**

#### ``set_authentication_token(auth_token)``

Set authentication token for given instance of OneTickLib

* **Parameters:**
  **auth_token** (*str*) – authentication token

#### ``static override_config_value(config_parameter_name, config_parameter_value)``

Override config value of OneTickConfig

* **Parameters:**
  * **config_parameter_name** (*str*) – param to override (could be both set or not set in OneTickConfig)
  * **config_parameter_value** – new value of the param
