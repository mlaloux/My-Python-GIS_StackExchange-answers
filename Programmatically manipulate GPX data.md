From [Programmatically manipulate GPX data](http://gis.stackexchange.com/questions/64818/programmatically-manipulate-gpx-data)

- osgeo.ogr can read all these formats: [OGR Vector Formats][1]
- osgeo.ogr and shapely support 3D:  

```python

        from osgeo import ogr
        point = ogr.Geometry(ogr.wkbPoint25D)
        point.AddPoint(5,4,4)
        point.GetZ()
        4.0

        from shapely.geometry import Point
        point1 = Point(5,4,4)
        point1.has_z
        True
        point1.z
        4.0
```


- you can change projections with osgeo.ogr: see [Projecting shapefile with transformation using OGR in python][2] and many, many other examples

- transform the geometries between ogr and shapely is easy:  

```python

        from shapely.wkb import loads
        point = ogr.Geometry(ogr.wkbPoint25D)
        point.AddPoint(5,4,4)
        point_shapely = loads(point.ExportToWkb())
        point_shapely.has_z
        True 
```
- inverse

```python

        point_ogr = ogr.CreateGeometryFromWkb(point_shapely.wkb)
        print point_ogr.GetX(), point_ogr.GetY(), point_ogr.GetZ()
        5.0 4.0 0.0
```
- so you can use ogr or pyproj to change the projection of a shapely geometry, (see [Measuring distance in spherical Mercator vs zoned UTM][3] for example)
- and shapely or analytical geometry allows to project a point on a PoLyline (see [How to draw perpendicular lines in QGIS?][4], with PyQGIS, but it is similar with ogr)

As one example of the process, here are the results of the creation of geological cross-sections from 3D points  (from [Python: Using vector and raster layers in a geological perspective, without GIS software][7], in French, but the scripts and the figures are universal).

3D representation (distance between points): 

![enter image description here][5]

cumulative distance (geological cross-section)

![enter image description here][6]
 




 


  [1]: http://www.gdal.org/ogr/ogr_formats.html
  [2]: http://gis.stackexchange.com/questions/19401/projecting-shapefile-with-transformation-using-ogr-in-python
  [3]: http://gis.stackexchange.com/questions/7293/measuring-distance-in-spherical-mercator-vs-zoned-utm
  [4]: http://gis.stackexchange.com/questions/59169/how-to-draw-perpendicular-lines-in-qgis/59196#59196
  [5]: http://i.stack.imgur.com/qaCB1.jpg
  [6]: http://i.stack.imgur.com/hwRDo.jpg
  [7]: http://www.portailsig.org/content/python-utilisation-des-couches-vectorielles-et-matricielles-dans-une-perspective-geologique-
