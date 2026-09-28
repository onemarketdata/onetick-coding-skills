# otp.LoadBalancing

### ``class LoadBalancing(*sockets)``

Bases: ``object``

Class representing configuration of client-side load-balancing between multiple tick servers.
Each client query selects the target tick server from the provided list according to the load-balancing policy.
Currently only SERVER_LOAD_WEIGHTED policy is available -  tick servers are chosen at random using a weighted
probability distribution, using inverted CPU usage for weights.
LoadBalancing class is used only with ``RemoteTS`` and
``FaultTolerance``.

* **Parameters:**
  **sockets** (*str*) – Sockets used in load-balancing configuration.

##### Examples

```
>>> LoadBalancing('host1:4001', 'host2:4002', 'host3:4003') 
```
