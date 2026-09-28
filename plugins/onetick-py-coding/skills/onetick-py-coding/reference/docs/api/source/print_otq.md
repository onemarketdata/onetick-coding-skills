# otp.Source.print_otq

#### ``Source.print_otq(**kwargs)``

Generate temporary .otq file with ``onetick.py.Source.to_otq()`` method,
print it to the standard output, then remove the file.

* **Parameters:**
  **kwargs** – Key-value arguments that are passed directly to ``onetick.py.Source.to_otq()``.

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data.print_otq()  
[query]
COMMENT =
CPU_NUMBER = 1
DB_HINT_FOR_PROCESSING_HOST =
NESTED_OTQS_USE_ONLY_SINKS_FOR_OUTPUT = TRUE
NO_COORDS = 1
NODE_1 = PASSTHROUGH
NODE_1_EP_PARAMETERS_FLAG = -2
NODE_1_SOURCE =  NODE_2
NODE_2 = TICK_GENERATOR(BUCKET_INTERVAL=0,BUCKET_TIME=BUCKET_START,FIELDS="long A=1")
NODE_2_EP_PARAMETERS_FLAG = -2
NODE_2_TICK_TYPE = DEMO_L1::ANY
one_to_many_symbol_mapping = 0
ROOT = PASSTHROUGH
ROOT_EP_PARAMETERS_FLAG = -2
ROOT_SOURCE =  NODE_1
SECURITY = AAPL 0
SHOW_TEMPLATE =
TYPE = GRAPH
...
```
