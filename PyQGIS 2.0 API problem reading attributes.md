From [](http://gis.stackexchange.com/questions/85110/pyqgis-2-0-api-problem-reading-attributes)

To know the available functions, in the Python console type:

```python
    dir(mylayer)
    # and
    dir(geom)

and one answer is (always in the Python console, slightly different with Processing or for a plugin):

```python
    fields = mylayer.pendingFields()
    field_names = [field.name() for field in fields]
    for feature in mylayer.getFeatures():
          geom= feature.geometry()
          point = geom.asPoint()
          print point
          attributes = features.attributes()
          print attributes
          dip_dir = feature['dip_dir']
          print dip_dir
          # with a dictionary
          atr = dict(zip(field_names, feature.attributes()))
          print atr
          print atr['dip_direct']

Result with one of my shapefiles

```python
     print field_names
     [u'dip_direct', u'dip', u'type']
     # some results of the for loop
     # geom.asPoint()
     (198236,89025.8)
     # attributes of the feature
     [180, 26, u'N']
     # dip_dir value
     180
     # attributes as dictionary
     {u'type': u'N', u'dip': 26, u'dip_direct': 180}
     # one particular value (dip_dir)
     180
     (199847,89197.1)
     [335, 50, u'N']
     335
     {u'type': u'N', u'dip': 50, u'dip_direct': 335}
     335
     etc.
```
and also:

```python

    # GeoJSON
    for ...
        print geom.exportToGeoJSON()
    { "type": "Point", "coordinates": [198235.93072444110293873, 89025.821821038480266] }
    { "type": "Point", "coordinates": [199847.19120459226542152, 89197.0735811617487343] }

```
or 

```python
     # WKT
     for ...
         print geom.exportToWkt()
     POINT(198235.93072444110293873 89025.821821038480266)
     POINT(199847.19120459226542152 89197.0735811617487343)
     
```
          
And a true [GeoJSON][1] with attributes ('properties'):

```python
     for elem in layer.getFeatures():
         geom= elem.geometry()
         atr = dict(zip(field_names, elem.attributes()))
         print dict(geometry=geom.exportToGeoJSON(),properties=atr)

      {'geometry': u'{ "type": "Point", "coordinates": [198235.93072444110293873, 89025.821821038480266] }', 'properties': {u'type': u'N', u'dip': 26, u'dip_direct': 180}}
      {'geometry': u'{ "type": "Point", "coordinates": [199847.19120459226542152, 89197.0735811617487343] }', 'properties': {u'type': u'N', u'dip': 50, u'dip_direct': 335}}
```

  [1]: http://geojson.org/geojson-spec.html
