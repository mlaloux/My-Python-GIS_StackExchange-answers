From [Serialize python list containing geometry](http://gis.stackexchange.com/questions/82850/serialize-python-list-containing-geometry)

Use the `mapping`function of shapely (and the reverse: `shape`):

```Python
    from shapely geometry import Point, mapping, shape
    point = Point(3,4)
    print mapping(point)
    {'type': 'Point', 'coordinates': (3.0, 4.0)}
    # and the reverse
    print shape(mapping(point))
    POINT (3.0000000000000000 4.0000000000000000)
```

Test:
    
```Python
    result = dict(fields="a", id=2, label="label", geom=mapping(point))
    geojson.dumps(result)
    '{"fields": "a", "geom": {"type": "Point", "coordinates": [3.0, 4.0]}, "id": 2, "label": "label"}'
    import json
    json.dumps(result)
    '{"fields": "a", "geom": {"type": "Point", "coordinates": [3.0, 4.0]}, "id": 2, "label": "label"}'
```

So, in your case:

```Python
    result = [dict(f.fields, id=f.id, label=f.label, geom=mapping(f.geom)) for f in features]
```

Look for examples of the use of [Fiona][1] on Gis StackExchange. This module uses the GeoJSON format to read and write shapefiles and other ogr formats..

```Python
    import fiona
    # schema of the shapefile
    schema = {'geometry': 'Point', 'properties': {'id':'int:2', 'name':'str'}}
    # write the shapefile with the shapely point
    with fiona.open('point.shp','w','ESRI Shapefile', schema) as w:
        w.write({'geometry':mapping(point), 'properties':{'id':1, 'name':'a point'}})
    # open and read the features of the shapefile
    features = fiona.open('point.shp')
    features.next()
    {'geometry': {'type': 'Point', 'coordinates': (3.0, 4.0)}, 'type': 'Feature', 'id': '0', 'properties': OrderedDict([(u'id', 1), (u'name', u'a point')])}

```

    




  [1]: http://toblerity.org/fiona/manual.html
