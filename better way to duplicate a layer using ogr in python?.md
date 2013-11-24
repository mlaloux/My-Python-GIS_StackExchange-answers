From [better way to duplicate a layer using ogr in python?](http://gis.stackexchange.com/questions/56703/better-way-to-duplicate-a-layer-using-ogr-in-python/56722)

Use [Fiona][1] of Sean Gillies , a very simple wrapper of the OGR library ([The Fiona User Manual][2])

All the elements of a shapefile (schema, records) are processed using Python dictionaries:

schema of one of my shapefiles as example:

> {'geometry': 'LineString', 'properties': {u'faille': 'str:20', u'type': 'str:20', u'id': 'int'}}

one record in the shapefile:

> {'geometry': {'type': 'LineString', 'coordinates': [(269884.20917418826, 151805.1917153612), (270409.89083992655, 153146.21637285672), (272298.05355768028, 154047.38494269375), (272941.74539327814, 155484.96337552898), (272169.31519056071, 156117.92701386689)]}, 'id': '1',  'properties': {'faille': u'de Salinas', 'type': u'normale'}}


so to duplicate a shapefile:

```python

    from shapely.geometry import mapping, shape
    import fiona
    # Read the original Shapefile
    with fiona.collection('original.shp', 'r') as input:
        # The output has the same schema
        schema = input.schema.copy()
        # write a new shapefile
        with fiona.collection(''file1.shp', 'w', 'ESRI Shapefile', schema) as output:
            for elem in input:
                 output.write({'properties': elem['properties'],'geometry': mapping(shape(elem['geometry']))})
```

If you want to split a large shapefile into many smaller ones, everything takes place in the for loop but all the schemas of the original shapefile are preserved in the dictionary with schema = input.schema.copy() and {'properties': elem['properties'] 

see [How do I find vector line bearing in QGIS or GRASS?][3] for an example of

 1. spliting a shapefile
 2. preserve the attributes of the original shapefile in the splitted shapefile
 3. and add a new field in the splitted shapefile

For Mac OS X or Linux users, it is easy to install. For Windows users, use the version of  Christoph Gohlke [Unofficial Windows Binaries for Python Extension Packages][4]


  [1]: https://pypi.python.org/pypi/Fiona/0.10
  [2]: http://toblerity.github.com/fiona/manual.html
  [3]: http://gis.stackexchange.com/questions/55449/how-do-i-find-vector-line-bearing-in-qgis-or-grass/55480#55480
  [4]: http://www.lfd.uci.edu/~gohlke/pythonlibs/#fiona
