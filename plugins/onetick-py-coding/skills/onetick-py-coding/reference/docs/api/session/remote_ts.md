# otp.RemoteTS

### ``class RemoteTS(host, port=None, protocol=None, resource=None, cep=False)``

Bases: ``object``

Class representing remote tick-server.
Can be ``used``
in ``Session`` as well as the local databases.

* **Parameters:**
  * **host** (str, ``LoadBalancing``, ``FaultTolerance``) – 

    In case of string: string with this format: `[proto://]hostname:port[/resource]`.
    Parameters in square brackets are optional.

    Otherwise, configuration of LoadBalancing and/or FaultTolerance (please, check corresponding classes)
  * **port** (*int* *,* *str* *,* *optional*) – The port number of the remote tick-server.
    If not specified here, can be specified in the `host` parameter.
  * **protocol** (*str* *,* *optional*) – The protocol to connect with.
    If not specified here, can be specified in the `host` parameter.
  * **resource** (*str* *,* *optional*) – The resource of the host.
    If not specified here, can be specified in the `host` parameter.
  * **cep** (*bool*) – Specifies if the remote server is the CEP-mode tick-server.

##### Examples

Specify host name and port together in first parameter:

```
>>> session.use(otp.RemoteTS('server.onetick.com:50015'))  
```

Additionally specify web-socket protocol and resource of the remote server:

```
>>> session.use(otp.RemoteTS('wss://data.onetick.com:443/omdwebapi/websocket'))  
```

Combination of LoadBalancing and FaultTolerance can be used for host parameter:

```
>>> RemoteTS(FaultTolerance(LoadBalancing('host1:4001', 'host2:4002'),
...                         LoadBalancing('host3:4003', 'host3:4004')) 
```
