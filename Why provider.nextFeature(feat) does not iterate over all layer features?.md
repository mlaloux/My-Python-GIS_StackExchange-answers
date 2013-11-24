
From [Why provider.nextFeature(feat) does not iterate over all layer features?](http://gis.stackexchange.com/questions/72359/why-provider-nextfeaturefeat-does-not-iterate-over-all-layer-features)

The while loop is not recommended in such cases, use a for loop, it  is easier:

 - a layer has n features -> layer.featureCount()
 - a feature has an id -> feature.id(), a geometry and attributes

with QGIS 1.8
--------
```Python
    layer = qgis.utils.iface.activeLayer()
    layer.select()
    for feature in layer:
        geom = feature.geometry()
        attrs = feature.attributeMap()
        # the result is a dictionary
        for atr in attrs.values():
            print '{0} de {1}: {2}'.format(feature.id(), layer.featureCount(), atr.toString())
```
Result with one of my examples:

```Python
    0 de 3: Aachen Formation
    0 de 3: AAC
    0 de 3: COU
    0 de 3: 175
    1 de 3: VAALS Formation
    1 de 3: VAA
    1 de 3: COU
    1 de 3: 185
    ....
```

with QGIS 2.0
-----------
```Python
     layer = qgis.utils.iface.activeLayer()
     for feature in layer.getFeatures():
         geom = feature.geometry()
         attrs = feature.attributes()
         # the result is a list
         for elem in attrs:
             print '{0} de {1}: {2}'.format(feature.id(), layer.featureCount(), elem)
```

and with a dictionary, you can access the field names:

```Python

    fields = layer.pendingFields()
    # name of the fields
    field_names = [field.name() for field in fields] 
    for feature in layer.getFeatures():
        atr = dict(zip(field_names, feature.attributes()))
        print atr
```
Result

```Python

    {u'FORM': u'AAC', u'DESCRIPTIO': u"Aachen Formation", u'CLASSE': u'COU',u'SYMBOL': 175.0}
    {u'FORM': u'AAC',  u'DESCRIPTIO': u"Vaals Formation", u'CLASSE': u'COU', u'SYMBOL': 185.0}
    ....
```
