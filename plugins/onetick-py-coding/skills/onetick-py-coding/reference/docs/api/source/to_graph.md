# otp.Source.to_graph

#### ``Source.to_graph(symbols=None, start=None, end=None, *, add_passthrough=True)``

Construct an `onetick.query.GraphQuery` object.

* **Parameters:**
  * **symbols** – symbols query to add to otq.GraphQuery
  * **start** (``otp.datetime``) – start time of a query
  * **end** (``otp.datetime``) – end time of a query
  * **add_passthrough** (*bool*) – add additional `onetick.query.Passthrough` event processor to the end of a resulted graph
* **Return type:**
  otq.GraphQuery

##### SEE ALSO
``render()``
