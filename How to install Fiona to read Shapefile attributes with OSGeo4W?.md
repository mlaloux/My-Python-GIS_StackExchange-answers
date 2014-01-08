 
 From [How to install Fiona to read Shapefile attributes with OSGeo4W?](http://gis.stackexchange.com/questions/82200/how-to-install-fiona-to-read-shapefile-attributes-with-osgeo4w/82207)
 
 1. Why would you want install [Fiona][1] if you can use PyQGIS to read the shapefile attributes ? 
 2. [Fiona][2] is for reading geometries **and** attributes of a shapefile file (as [PyShp][3]), therefore you don't need [dbfpy][4] (look at [Python Script examples for geoprocessing shapefiles without using arcpy][5]).
 3. [Fiona][6] is a Python module so you must install it as any Python module in the site-packages folder of your Python installation as SaultDon says.
 4. but as [Fiona][7] needs to compile C++ code (from GDAL/OGR) to install, you cannot use `pip` or `easy-install` on Windows (no compiler).You can try to install the "ready to use" version of [Christoph Gohlke][8] but it is linked to his version of [GDAL][9])
 5. this procedure works with the standalone version of QGIS.

###Examples of reading attributes of a shapefile:

With Fiona:

```python

    import fiona
    features = fiona.open("strati.shp")
    features.schema
    {'geometry': 'Point', 'properties': OrderedDict([(u'PENDAGE', 'int:2'), (u'DIRECTION', 'int:3'), (u'TYPE', 'str:10')])}
    for feat in features:
         print feat['properties']

    OrderedDict([(u'PENDAGE', 30), (u'DIRECTION', 120), (u'TYPE', u'incl')])
    OrderedDict([(u'PENDAGE', 45), (u'DIRECTION', 145), (u'TYPE', u'incl')])
    OrderedDict([(u'PENDAGE', 78), (u'DIRECTION', 148), (u'TYPE', u'incl')])
    
```

With PyShp:

```python

    import shapefile
    reader = shapefile.Reader("strati.shp") 
    fields = reader.fields[1:]
    print fields
    [['PENDAGE', 'N', 2, 0], ['DIRECTION', 'N', 3, 0], ['TYPE', 'C', 10, 0]]
    field_names = [field[0] for field in fields]
    schema = dict((d[0],d[1:]) for d in reader.fields[1:])
    print schema
    {'DIRECTION': ['N', 3, 0], 'PENDAGE': ['N', 2, 0], 'TYPE': ['C', 10, 0]}
    for feat in reader.shapeRecords():
        print dict(zip(field_names, sr.record))  

    {'DIRECTION': 148, 'PENDAGE': 78, 'TYPE': 'incl'}
    {'DIRECTION': 148, 'PENDAGE': 78, 'TYPE': 'incl'}
    {'DIRECTION': 148, 'PENDAGE': 78, 'TYPE': 'incl'}

```

with PyQGIS in the Python console:

```python

    layer = qgis.utils.iface.activeLayer()  
    fields = layer.pendingFields()  
    field_names = [field.name() for field in fields]  
    field_types = [field.typeName() for field in fields]
    field_precision = [field.precision() for field in fields]
    print field_precision
    [0,0,0]
    print dict(zip(field_names,field_types))
    {u'DIRECTION': u'Integer', u'PENDAGE': u'Integer', u'TYPE': u'String'}
    schema = dict((field_names[i], {field_types[i]:field_precision[i] if  field_precision[i] > 0 else field_types[i]:'' }) for i in range(len(field_names)))
    print schema
    {u'DIRECTION': {u'Integer': ''}, u'PENDAGE': {u'Integer': ''}, u'TYPE': {u'String': ''}}
    for feat in layer.getFeatures():  
        print dict(zip(field_names, feat.attributes()))
 
    {u'DIRECTION': 120, u'PENDAGE': 30, u'TYPE': u'incl'}
    {u'DIRECTION': 145, u'PENDAGE': 45, u'TYPE': u'incl'}
    {u'DIRECTION': 148, u'PENDAGE': 78, u'TYPE': u'incl'} 

```

With dbfpy:

```python


    from dbfpy import dbf
    db = dbf.Dbf("strati.dbf")
    print db.fieldNames
    ['PENDAGE', 'DIRECTION', 'TYPE']
    print db.fieldDefs
    [PENDAGE    N   2   0, DIRECTION  N   3   0, TYPE       C  10   0]  
    schema = dict(zip(db.fieldNames,db.fieldDefs))
    print schema
    {'DIRECTION': DIRECTION  N   3   0, 'PENDAGE': PENDAGE    N   2   0, 'TYPE': TYPE       C  10   0} 
    for feat in db:
        print dict(zip(db.fieldNames, feat.asList()))  

    {'DIRECTION': 120, 'PENDAGE': 30, 'TYPE': 'incl'}
    {'DIRECTION': 145, 'PENDAGE': 45, 'TYPE': 'incl'}
    {'DIRECTION': 148, 'PENDAGE': 78, 'TYPE': 'incl'}

```

So you don't need dbfpy or other dbf Python module ([PyPI:dbf][10])


  [1]: http://toblerity.org/fiona/manual.html
  [2]: http://toblerity.org/fiona/manual.html
  [3]: http://code.google.com/p/pyshp/
  [4]: https://pypi.python.org/pypi/dbfpy/2.2.0
  [5]: http://gis.stackexchange.com/questions/64146/python-script-examples-for-geoprocessing-shapefiles-without-using-arcpy/64158#64158
  [6]: http://toblerity.org/fiona/manual.html
  [7]: http://toblerity.org/fiona/manual.html
  [8]: http://www.lfd.uci.edu/~gohlke/pythonlibs/#fiona
  [9]: http://www.lfd.uci.edu/~gohlke/pythonlibs/#gdal
  [10]: https://pypi.python.org/pypi?:action=search&term=dbf&submit=search
