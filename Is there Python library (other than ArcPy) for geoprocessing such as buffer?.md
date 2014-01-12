From [Is there Python library (other than ArcPy) for geoprocessing such as buffer?](http://gis.stackexchange.com/questions/82521/is-there-python-library-other-than-arcpy-for-geoprocessing-such-as-buffer/82555#82555)

To simplify, [Shapely: manual][1]  allows all geometry processing of PostGIS in Python.

>The first premise of Shapely is that Python programmers should be able to perform PostGIS type geometry operations outside of an RDBMS...

The first example of PolyGeo

```python 

    from shapely.geometry import Point, LineString, Polygon, mapping
    from shapely.wkt import loads  
    pt = Point(1198054.34,648493.09)
    # or
    pt = loads("POINT (1198054.34 648493.09)")
    bufferDistance = 500
    poly = pt.buffer(bufferDistance)
    print poly.wkt
    'POLYGON ((1198554.3400000001000000 648493.0899999999700000, 1198551.9323633362000000 
    # GeoJSON
    print mapping(poly)
    {'type': 'Polygon', 'coordinates': (((1198554.34, 648493.09), (1198551.9323633362, 648444.0814298352), (1198544.7326402017, 648395.544838992), ....}

```

The polygon's example from PolyGeo:

```python 

    poly1 = Polygon([(1208064.271243039,624154.6783778917), (1208064.271243039,601260.9785661874), (1231345.9998651114,601260.9785661874),(1231345.9998651114,624154.6783778917),(1208064.271243039,624154.6783778917)])    
    poly2 = loads("POLYGON ((1199915.6662253144 633079.3410163528, 1199915.6662253144 614453.958118695, 1219317.1067437078 614453.958118695, 1219317.1067437078 633079.3410163528, 1199915.6662253144 633079.3410163528)))"

    intersection = poly1.intersection(poly2)
    print intersection.wkt
    print mapping(intersection) -> GeoJSON
    
```

> The second premise is that the persistence, serialization, and map projection of features are significant, but orthogonal problems. You may not need a hundred GIS format readers and writers or the multitude of State Plane projections, and Shapely doesn’t burden you with them.

So you combine it with other Python modules to read or write shapefiles and manipulate projections as osgeo.ogr,  [Fiona][2] or [PyShp][3].  
Searching in Gis StackExchange, you can find many examples but I give you another one to illustrate the  combination of shapely and Fiona and the use of the shapely functions intersection() and buffer() (This could have been done with PyShp).

Given two polyline shapefiles:

![enter image description here][4]

Compute the intersection (function intersection() of shapely)

```python 

    from shapely.geometry import Point, Polygon, MultiPolygon, MumtiPoint, MultiLineString,shape, mapping
    import fiona
    # read the shapefiles and transform to MultilineString shapely geometry (shape())
    layer1 = MultiLineString([shape(line['geometry']) for line in fiona.open('polyline1.shp')])  
    layer2 = MultiLineString([shape(line['geometry']) for line in fiona.open('polyline2.shp')])
    points_intersect = layer1.intersection(layer2)
```
    
Save the result as a new shapefile

```python 

    # schema of the new shapefile
    schema = {'geometry': 'MultiPoint','properties': {'test': 'int'}}
    # write the new shapefile (function mapping() of shapely)
    with fiona.open('intersect.shp','w','ESRI Shapefile', schema) as e:
      e.write({'geometry':mapping(points_intersect), 'properties':{'test':1}})
```

Result:

![enter image description here][5]

Buffer individual points (function buffer() of shapely)

```python 

     # new schema
     schema = {'geometry': 'Polygon','properties': {'test': 'int'}}
     with fiona.open('buffer.shp','w','ESRI Shapefile', schema) as e:
         for point in points:
              e.write({'geometry':mapping(point.buffer(300)), 'properties':{'test':1}})
```

Result

![enter image description here][6]

Buffer the MultiPoint geometry

```python 

    schema = {'geometry': 'MultiPolygon','properties': {'test': 'int'}}
    points.buffer(300)
    with fiona.open('buffer2.shp','w','ESRI Shapefile', schema) as e:
         e.write({'geometry':mapping(points.buffer(300)), 'properties':{'test':1}})
```

![enter image description here][7]


  [1]: http://toblerity.org/shapely/manual.html
  [2]: http://toblerity.org/fiona/manual.html
  [3]: http://code.google.com/p/pyshp/
  [4]: http://i.stack.imgur.com/bb6HE.jpg
  [5]: http://i.stack.imgur.com/jryaY.jpg
  [6]: http://i.stack.imgur.com/NTtsd.jpg
  [7]: http://i.stack.imgur.com/WnC9M.jpg
