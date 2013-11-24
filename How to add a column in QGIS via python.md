From [How to add a column in QGIS via python](http://gis.stackexchange.com/questions/56826/how-to-add-a-column-in-qgis-via-python)


If you want to use Python, you don't need QGIS, except if you want to create a plugin. 
In this case, you should consider PyQGIS with the reference given by Curlew

But you can also use Python modules like [pyshp][1], [osgeo (gdal and ogr][2]) or [Fiona][3] and [Shapely][4] without QGIS

In both cases, you need a join field that will link the polygon shapefile to the point shapefile.

**Example with Fiona and Shapely** (all the elements of a shapefile (schema,geometry, records) are processed using Python dictionaries). 

With ogr and Fiona it is easier to create a new shapefile, copying the original shapefile (geometry and attributes), and adding new fields with the desired values than modify the original shapefile.

```python

    from shapely.geometry import mapping
    import fiona
    # open the polygon shapefile
    with fiona.collection('polygon.shp', 'r') as polygon:
        # copy of the schema of the original polygon shapefile to the output shapefile (copy)
        schema = polygon.schema.copy()
        # creation of the new field color in the new schema
        schema['properties']['color'] = 'str' 
            # output shapefile with the new schema
            with fiona.collection('join_poly_pt.shp', 'w', 'ESRI Shapefile', schema) as output:
                # open the point shapefile with colors
                with fiona.collection('point.shp', 'r') as points:
                     polygons = [elem for elem in polygon]
                     points = [elem for elem in point]
                     # joint
                     for poly in polygons:
                         for pt in points:
                             # common field for the join
                             if poly['properties']['test'] == pt['properties']['test']:
                                 # construction of the new shapefile
                                 res = {}                  
                                 res['properties'] = poly['properties'] 
                                 res['properties']['color'] = pt['properties']['color'] 
                                 # geometry of of the original polygon shapefile
                                 res['geometry'] = mapping(shape(poly['geometry']))
                                 output.write(res)
```

simple example
![enter image description here][5]



  [1]: https://pypi.python.org/pypi/pyshp/1.1.4
  [2]: https://pypi.python.org/pypi/GDAL/1.9.1
  [3]: https://pypi.python.org/pypi/Fiona/0.10
  [4]: https://pypi.python.org/pypi/Shapely/1.2.17
  [5]: http://i.stack.imgur.com/xpfbd.jpg
