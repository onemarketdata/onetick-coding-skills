# otp.FaultTolerance

### ``class FaultTolerance(*sockets)``

Bases: ``object``

Class representing configuration of client-side tick-server fault tolerance.
Tick servers in the fault tolerance list are selected according to their level of priority,
which is from left to right. Backup servers (servers after the first one, primary) are used only if the query
on the primary server failed or server is down. So, if the first server is down the second server is used,
if both of them are failed the third one used and so on.
FaultTolerance class is used only with ``RemoteTS``.

* **Parameters:**
  **sockets** (*str* *,* *LoadBalancing*) – Sockets and/or LoadBalancing groups used in fault tolerance configuration.

##### Examples

```
>>> FaultTolerance('host1:4001', 'host2:4002', 'host3:4003') 
```

It is possible to have multiple load-balancing groups thus mixing together fault tolerance and load-balancing.
The selection between different load balancing groups is also done according to their priority level,
which is from left to right.

```
>>> FaultTolerance(LoadBalancing('host1:4001', 'host2:4002'),
...                LoadBalancing('host3:4003', 'host3:4004')) 
```
