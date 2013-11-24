From [How to get field names in pyqgis 2.0](http://gis.stackexchange.com/questions/76364/how-to-get-field-names-in-pyqgis-2-0)

It is simpler than with version 1.8:
```python
    layer = qgis.utils.iface.activeLayer()  
    fields = layer.pendingFields()   
    field_names = [field.name() for field in fields]
```

or in one line
```python
    field_names = [field.name() for field in layer.pendingFields() ]
```
an after   
```python

    for elem in layer.getFeatures():  
       print dict(zip(field_names, elem.attributes()))
    {u'adip': 17, u'dipdir': 130, u'tdip': 29}
    {u'adip': 55, u'dipdir': 325, u'tdip': 75}
    .....
```

Generally to explore a new function, I use the dir()  or the [see][1] module to examine what's inside:

```python

    dir(fields)
    ['FieldOrigin', 'OriginEdit', 'OriginJoin', 'OriginProvider', 'OriginUnknown', '__class__', '__delattr__', '__delitem__', '__dict__', '__doc__', '__format__', '__getattribute__', '__getitem__', '__hash__', '__init__', '__len__', '__module__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'append', 'at', 'clear', 'count', 'extend', 'field', 'fieldOrigin', 'fieldOriginIndex', 'indexFromName', 'isEmpty', 'remove', 'size', 'toList']
```  
or 

```python

    see(fields)
    []                 hash()             help()             len()
    repr()             str()              .FieldOrigin()     .OriginEdit
    .OriginJoin        .OriginProvider    .OriginUnknown     .append()
    .at()              .clear()           .count()           .extend()
    .field()           .fieldOrigin()     .fieldOriginIndex()
    .indexFromName()   .isEmpty()         .remove()          .size()
    .toList()
    
```

And you can see that there is a field() function
so 

```python

    print fields.field(0)
    qgis.core.QgsField object at 0x163E39C0
```


and a field has a name, a type, a precision,...:

```python
    see(fields.field(0))
    <                 <=                ==                !=                >
    >=                hash()            help()            repr()
    str()             .comment()        .displayString()  .length()
    .name()           .precision()      .setComment()     .setLength()
    .setName()        .setPrecision()   .setType()        .setTypeName()
    .type()           .typeName()
```
so: 

```python
    fields.field(0).name()
    u'adip'
    fields.field(0).type()
    2
    fields.field(0).typeName()
    u'Integer'
    fields.field(0).precision()
    0
    ....
```

----------------------------------
#with .dataProvider()

It is .field()

```python
    ....
    prov = layer.dataProvider()
    prov.fields().field(0).name()
    u'adip'
    prov.fields().field(0).typeName()
    u'Integer'
```

and 

```python
   
    field_names = [field.name() for field in prov.fields()]
```


  [1]: https://github.com/inky/see
