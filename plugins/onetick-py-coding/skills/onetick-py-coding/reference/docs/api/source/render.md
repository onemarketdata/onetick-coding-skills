# otp.Source.render

#### ``Source.render(**kwargs)``

Renders a calculation graph using the `graphviz` library.
Every node is the onetick query language event processor.
Nodes in nested queries, first stage queries and eval queries are not shown.
Could be useful for debugging and in jupyter to learn the underlying graph.

Note that it’s required to have `graphviz` package installed.

##### Examples

```
>>> data = otp.Tick(X=3)
>>> data1, data2 = data[(data['X'] > 2)]
>>> data = otp.merge([data1, data2])
>>> data.render()  
```
