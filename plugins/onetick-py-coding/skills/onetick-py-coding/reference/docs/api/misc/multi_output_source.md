# otp.MultiOutputSource

### *class* MultiOutputSource

Bases: ``object``

Construct a source object with multiple outputs
from several connected ``Source`` objects.

This object can be saved to disk as a graph using ``to_otq()`` method,
or passed to ``onetick.py.run()`` function.

If it’s passed to ``onetick.py.run()``,
then returned results for different outputs will be available as a dictionary.

* **Parameters:**
  **outputs** (*dict*) – Dictionary which keys are names of the output sources, and values are output sources themselves.
  All the passed sources should be connected.

##### Examples

Results for individual outputs can be accessed by output names

```
>>> root = otp.Tick(A=1)
>>> branch_1 = root.copy()
>>> branch_2 = root.copy()
>>> branch_3 = root.copy()
>>> branch_1['B'] = 1
>>> branch_2['B'] = 2
>>> branch_3['B'] = 3
>>> src = otp.MultiOutputSource(dict(BRANCH1=branch_1, BRANCH2=branch_2, BRANCH3=branch_3))
>>> res = otp.run(src)
>>> sorted(list(res.keys()))
['BRANCH1', 'BRANCH2', 'BRANCH3']
>>> res['BRANCH1'][['A', 'B']]
   A  B
0  1  1
>>> res['BRANCH2'][['A', 'B']]
   A  B
0  1  2
>>> res['BRANCH3'][['A', 'B']]
   A  B
0  1  3
```

node_name parameter of the otp.run() method can be used to select outputs

```
>>> src = otp.MultiOutputSource(dict(BRANCH1=branch_1, BRANCH2=branch_2, BRANCH3=branch_3))
>>> res = otp.run(src, node_name=['BRANCH2', 'BRANCH3'])
>>> sorted(list(res.keys()))
['BRANCH2', 'BRANCH3']
>>> res['BRANCH2'][['A', 'B']]
   A  B
0  1  2
>>> res['BRANCH3'][['A', 'B']]
   A  B
0  1  3
```

If only one output is selected, then it’s returned directly and not in a dictionary

```
>>> src = otp.MultiOutputSource(dict(BRANCH1=branch_1, BRANCH2=branch_2, BRANCH3=branch_3))
>>> res = otp.run(src, node_name='BRANCH2')
>>> res[['A', 'B']]
   A  B
0  1  2
```

A dictionary with sources can also be passed to otp.run directly,
and MultiOutputSource object will be constructed internally

```
>>> res = otp.run(dict(BRANCH1=branch_1, BRANCH2=branch_2))
>>> res['BRANCH1'][['A', 'B']]
   A  B
0  1  1
>>> res['BRANCH2'][['A', 'B']]
   A  B
0  1  2
```

#### ``get_branch(branch_name)``

Retrieve a branch by its name.

* **Parameters:**
  **branch_name** (*str*) – The name of the branch to retrieve.
* **Returns:**
  The branch corresponding to the given name.
* **Return type:**
  otp.Source

#### ``property main_branch *: `Source`*``

Get the main branch.

* **Returns:**
  The main branch.
* **Return type:**
  otp.Source

#### ``to_otq(file_name=None, file_suffix=None, query_name=None, symbols=None, start=None, end=None, timezone=None)``

Constructs an onetick query graph and saves it to disk

##### SEE ALSO
``onetick.py.Source.to_otq()``
