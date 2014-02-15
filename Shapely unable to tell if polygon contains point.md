From [Shapely unable to tell if polygon contains point](http://gis.stackexchange.com/questions/84114/shapely-unable-to-tell-if-polygon-contains-point)

If we examine your polygon:

```Python
    polygon = shapefile_record['geometry']
    print polygon.bounds
    (77.84476181915733, 30.711096140487314, 78.59476181915738, 31.28199614048725)

```

From [Shapely manual][1], `object.bounds`:

> Returns a (minx, miny, maxx, maxy) tuple (float values) that bounds the object.

Here minx = 77.84476181915733, miny = 30.711096140487314

= here, min longitude = 77.844... and min latitude = 30.711...

Setting your point as Point(30.9787, 78.8900) or Point(latitude, longitude), you reverse the coordinates:


```Python
    your_point = Point(30.9787, 78.8900) # = Point(y, x)
    xy_point = Point(78.8900,30.9787) # = Point(x,y)
    polygon.contains(xy_point)
    False

```

Which is correct:

![enter image description here][2]

So, the script with your example:


```Python

    from shapely.geometry import Point, shape
    point = Point(78.8900,30.9787)
    fc = fiona.open("AC_All_Final.shp")
    print fc.schema
    {'geometry': 'Polygon', 'properties': OrderedDict([(u'DIST_NAME', 'str:254'), (u'Assem_No', 'int:4'), (u'AREA', 'float:18.3'), (u'PERIMETER', 'float:18.3'), (u'INDIAASSEM', 'float:11'), (u'INDIAASS', 'float:11'), (u'NO', 'float:11'), (u'ST_CODE', 'str:254'), (u'AC_NAME', 'str:254'), (u'AC_TYPE', 'str:254'), (u'PC_NO', 'float:11'), (u'AC_NO', 'float:19.11'), (u'AC_HNAME', 'str:254'), (u'PARTY', 'str:254'), (u'CODE_NO', 'str:254'), (u'Longitude', 'float:19.17'), (u'Latitude', 'float:19.17'), (u'State', 'str:50'), (u'PC_NAME', 'str:25'), (u'PC_TYPE', 'str:3'), (u'PC_CODE', 'str:10'), (u'PC_NO_1', 'int:4')])}
    from feature in fc:
        if shape(feature['geometry']).contains(point):
              print feature['properties']['DIST_NAME'], feature['properties']['Longitude'], feature['properties']['Latitude']

    Uttarkashi 78.8900584624 30.9787742252


```

    
Result:

![enter image description here][3]


   


  [1]: http://toblerity.org/shapely/manual.html
  [2]: http://i.stack.imgur.com/tWFpx.jpg
  [3]: http://i.stack.imgur.com/xMJXD.jpg
