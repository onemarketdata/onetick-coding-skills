# otp.Source.dump

#### ``Source.dump(label=None, where=None, columns=None, callback=None)``

Dumps the `columns` from ticks into std::out in runtime if they fit the `where` condition.
Every dump has a corresponding header that always includes the TIMESTAMP field. Other fields
could be configured using the `columns` parameter. A header could be augmented with a `label` parameter;
this label is an addition column that helps to distinguish ticks
from multiple dumps with the same schema, because ticks from different dumps could be mixed.
It might happen because of the OneTick multithreading, and there is the operating system
buffer between the OneTick and the actual output.

This method is helpful for debugging.

* **Parameters:**
  * **label** (*str*) – A label for a dump. It adds a special column **\_OUT_LABEL_** for all ticks and set to the
    specified value. It helps to distinguish ticks from multiple dumps, because actual
    output could contain mixed ticks due the concurrency. `None` means no label.
  * **where** (*Operation*) – A condition that allows to filter ticks to dump. `None` means no filtering.
  * **columns** (*str* *,* *tupel* *or* *list*) – List of columns that should be in the output. `None` means dump all columns.
  * **callback** (*callable*) – Callable, which preprocess source before printing.
  * **self** (*Source*)

##### Examples

```
>>> data.dump(label='Debug point', where=data['PRICE'] > 99.3, columns=['PRICE', 'QTY'])    
>>> data.dump(columns="X", callback=lambda x: x.first(), label="first")     
```

##### SEE ALSO
**WRITE_TEXT** OneTick event processor
