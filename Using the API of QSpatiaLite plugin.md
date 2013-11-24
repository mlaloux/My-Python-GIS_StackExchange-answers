from [Using the API of QSpatiaLite plugin]

If you use Python, you can perfectly use one of the modules that can read shapefiles like ogr, Fiona or Pyshp to extract the geometries and the attributes of a shapefile and insert them into SQLite/SpatiaLite with pyspatialite without QGIS and QSpatiaLite.

With [pyshp][1] (result = Python lists)

```python

    import shapefile
    input = shapefile.Reader("joinpolypt.shp")
    # fields
    input.fields[1:]
    [['test', 'N', 1, 0], ['color', 'C', 80, 0]]
    # geometries and attributes of the layer
    shapes = input.shapes()
    attributes = input.records()
    # first record
    shapes[0].points[0]
    [273781.244220372, 154668.35103545961]
    attributes[0]
    [1, 'red']
    .....
```


With [Fiona][2] (result = Python dictionaries):

```python

    import fiona
    points = fiona.open('testpoint.shp')
    # schema of the shapefile
    points.schema
    {'geometry': 'Point', 'properties': {u'test': 'int', u'color': 'str'}}
    # first point
    points.next()
    {'geometry': {'type': 'Point', 'coordinates': (273781.244220372, 154668.35103545961)}, 'id': '0', 'properties': {'test': 1, 'color': u'red'}}
     ...
```

It is similar with ogr.

In all the cases you get the geometries and the attributes as lists or dictionaries 

Then it is easy to use pyspatialite to incorporate these values ​​in a spatialite database. 

Note that with OGR 1.10, the GDAL/OGR library can be loaded as a SQLite extension (like spatialite) and you can use directly Python and ogr ([SQLite / Spatialite RDBMS][3])


  [1]: http://code.google.com/p/pyshp/
  [2]: https://pypi.python.org/pypi/Fiona
  [3]: http://www.gdal.org/ogr/drv_sqlite.html
