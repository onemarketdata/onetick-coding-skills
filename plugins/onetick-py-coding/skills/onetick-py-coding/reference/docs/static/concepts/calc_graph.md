# Calculation graph

Every source holds the underlying calculation graph.
Operations on sources are not executed right away but are recorded as nodes in the calculation graphs.

#### NOTE
The word “operation” here refers to any operations on the data: e.g., calling a method/function or using
operators like ‘+’, ‘=’. It does not refer to the ``onetick.py.Operation`` class that is a data structure
generalizing a column of data (see `Data structures and functions`).

A newly created source normally contains only one initial node in its calculation graph.

For example

<!-- # to allow schema work properly in examples
>>> session.dbs['SOME_DB'].add(otp.Tick(X=1), tick_type='TT', symbol='S1') -->
```
>>> data = otp.DataSource(db='SOME_DB', tick_type='TT', symbol='S1')
```

has one initial node

An operation on the source adds a new node into the
calculation graph. For example the following line

```
>>> data['Y'] = data['X'] * 2
```

extends the existing graph

#### NOTE
We use descriptive content in the nodes just to illustrate what happens under the hood. Real nodes contain
OneTick-supported operations. Some operations add more than one node.

Some operations produce a new source. The original source is immutable.

#### NOTE
For simplicity we use the name `source` even when the object refers to a number of operations applied to the
source data. In our parlance every node in a graph is a source.

The new source holds a copy of the original calculation graph plus the operation related nodes. In the example below

```
>>> aggregated_data = data.agg({'SUM_X': otp.agg.sum(data['X']),
...                            'NUM_TICKS': otp.agg.count()})
```

the graph for the `data` object is the same as before, but the graph for the `aggregated_data` is

Functions/methods that operate on multiple sources (e.g., ``onetick.py.merge()`` or ``onetick.py.join_by_time()``)
return a new calculation graph created based on the passed ones

```
>>> merged_data = otp.merge([aggregated_data, data])
>>> merged_data['Z'] = 0   # add a new column just for illustration
```

The common nodes are collapsed to reduce the number of nodes in the graph. This mechanism is called as *graphs gluing*.
More about it is in `Graphs gluing`.

Operations that split a source return multiple output sources each with a copy of the original
calculation graph.

## Graphs gluing

Gluing two calculation graphs using join or merge operations raises a question of possible common ancestor operations
assigned on the ticks flow.
In that case it would be good to collapse them (from many points of view: fewer operations to process, easier to
debug, etc) to reduce the number of operations (i.e., nodes in the final graph). It means that we need to determine
somehow that graphs have *the same operations*.

Every new operation on a source has a *unique id* that we’ve introduced to understand further whether
the operations are the same and we could collapse them. We copy these ids when we copy a source along with the
underlying calculation graph. In other words, a copy of a source contains not only a copy of the calculation graph,
but also a set of ids for every operation. Then in case of joining, for example, we check ids
of every source and collapse the same nodes; the same id means that the operations are the same.

All operations on sources implicitly use the ``onetick.py.Source.copy()`` method. A user could explicitly call it.
Let’s use it to illustrate what we are talking about.

```
>>> data = otp.DataSource(db='SOME_DB', tick_type='TT', symbol='S1')
>>> data_c = data.copy()
>>> data['X'] = 1
>>> data_c['X'] = 2
>>> res = otp.merge([data, data_c])
```

the `res` object has the following calculation graph

when the `data_c` has

Some special cases require explicitly to have a *full* copy of the object even for operation ids.
For that case we have the ``onetick.py.Source.deepcopy()`` method

```
>>> data = otp.DataSource(db='SOME_DB', tick_type='TT', symbol='S1')
>>> data_c = data.deepcopy()
>>> data['X'] = 1
>>> data_c['X'] = 2
>>> res = otp.merge([data, data_c])
```

In that case the `res` object has the following calculation graph:
