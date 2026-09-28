# Symbol Parameters Objects

Note that these objects are internal and can’t be created manually.
They can only be returned with other onetick.py functions or methods, e.g. ``onetick.py.Source.to_symbol_param()``

### *class* \_SymbolParamSource

Bases: ``object``

Internal container that provides access to symbol parameters.
The object is read-only, you can only get symbol parameters with it.

#### ``property schema``

Represents actual python data schema in the column name -> type format.

#### \_\_getitem_\_(key)

Get symbol parameter with corresponding `key`.
Raises an error if such column does not exist.

### *class* \_SymbolParamColumn

Bases: ``Column``

Internal object representing OneTick’s symbol parameters.
Can be used in other onetick.py methods to specify if object is symbol parameter.
