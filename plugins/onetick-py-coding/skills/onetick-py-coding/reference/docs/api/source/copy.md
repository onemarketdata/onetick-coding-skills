# otp.Source.copy

#### ``Source.copy()``

Build an object with copied calculation graph.

Every node of the resulting graph has the same id as in the original. It means that
if the original and copied graphs are merged or joined together further then all common
nodes (all that created before the .copy() method) will be glued.

For example, let’s imagine that you have the following calculation graph `G`

where `A` is a source and `B` is some operation on it.

Then we copy it to the `G'` and assign a new operation there

After that we decided to merge `G` and `G'`. The resulting calculation graph will be:

Please use the ``Source.deepcopy()`` if you want to get the following calculation graph after merges and joins

* **Return type:**
  `Source`

##### SEE ALSO
``Source.deepcopy``
