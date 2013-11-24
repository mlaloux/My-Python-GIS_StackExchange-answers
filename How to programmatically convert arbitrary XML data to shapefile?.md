From [How to programmatically convert arbitrary XML data to shapefile?](http://gis.stackexchange.com/questions/71182/how-to-programmatically-convert-arbitrary-xml-data-to-shapefile)


I wrote the reply at the same time as blah238 so I modified it consequently.

The problem is more complex because you need to:

 - "learn" the structure of the [XML][1] file to extract the data as:
      - lat="35" lon="-180" from  the location tag = the vertices of the polygon (y, x);


 - rebuild the polygon in a text format with: 
    
      - text or list: (-180, 35), (-170, -33),..., (-153, -30)
      - [WKT][2]: ('POLYGON ((-180 35, -170 -33, ..., -153 -30))' ) 
      - [GeoJSON ][3]: ({"type": "Polygon", "coordinates": [[ [-180.0, 35.0], ..., [-153.0, -30.0]]]})  
 
I do not know what application could do so. They generally process [XML][4] files with known schemas.

The only solution I see is with a Python script as blah238 propose:

 1. process the XML file with a module like [ElementTree][5] (see blah238 script above)

 2. build the geometry and create the shapefile with modules like: 

[Pyshp][6] (another solution with directly a Polygon)

```python


    import shapefile
    w = shapefile.Writer(shapefile.POLYGON)
    # geometry
    w.poly(parts=[[[-180, 35],[-170, -33],..,[-153, -30]]])
    # fields
    w.field('FIRST_FLD','C','40')
    ....
    w.record('one', )
    w.save('polygon')
```

[Fiona][7] and [shapely][8] or [PyGeoif][9]

```python


    import fiona
    from shapely.geometry import Polygon, mapping
    polygon = Polygon([(-180, 35), (-170, -33),..., (-153, -30)])
    # schema of the shapefile
    schema = {'geometry': 'Polygon','properties': {'FIRST_FLD': 'char'}}
    # write the shapefile
    with fiona.collection('polygon.shp', 'w', 'ESRI Shapefile', schema) as layer:
        feature = {}
        feature['geometry'] = mapping(polygon) 
        feature['properties'] = {'FIRST_FLD': ...}
        layer.write(feature)
```

[Osgeo (GDAL/OGR)][10]

see [Python GDAL/OGR Cookbook 1.0 documentation ][11]

You have the choice

  [1]: http://en.wikipedia.org/wiki/XML
  [2]: http://en.wikipedia.org/wiki/Well-known_text
  [3]: http://www.geojson.org/geojson-spec.html
  [4]: http://en.wikipedia.org/wiki/XML
  [5]: http://docs.python.org/2/library/xml.etree.elementtree.html
  [6]: http://code.google.com/p/pyshp/
  [7]: http://toblerity.org/fiona/manual.html
  [8]: http://toblerity.org/shapely/manual.html
  [9]: https://github.com/cleder/pygeoif
  [10]: https://pypi.python.org/pypi/GDAL/1.10.0
  [11]: http://pcjericks.github.io/py-gdalogr-cookbook/geometry.html#create-a-polygon
