From [How to create a 3D shapefile from a raster?]


For the moment, the geometry system of QGIS does not handle z-values but z-coordinates can be read by parsing the binary format of the geometry (see [Why is it not possible to extract the z value of a wkb geometry with PyQGIS while it is possible with ogr or Shapely ?][1]) of which I take the example:

With a 3D point shapefile in the Python console of QGIS:

```python

    mylayer = qgis.utils.iface.activeLayer()
    mylayer.geometryType() == QGis.Point
    True
    # select the first object
    sel = mylayer.selectedFeatures()[0]
    qgisgeom = sel.geometry()
    qgisgeom.wkbType() == QGis.WKBPoint
    False
    qgisgeom.wkbType() == QGis.WKBPoint25D
    True
    # QGIS recognizes that the WKB geometry is 3D but is is impossible to extract the z value 
    qgisgeom.asPoint()
    (205553,89857.7)
    # no z in the functions of geom.asPoint, only x and y in dir()
    dir(qgisgeom.asPoint())
    ['__class__', ..., 'x', 'y']
```
    
But using other Python modules like osgeo.ogr or Shapely, you can extract the z value from the binary format of the QGIS geometry:

```python

    # with shapely
    from shapely.wkb import loads
    wkb = qgisgeom.asWkb()
    transfshapely = loads(wkb)
    transfshapely.has_z
    True
    list(transfshapely.coords)
    [(205552.628666262, 89857.66932836606, 222.1999969482422)]

    # with osgeo.ogr
    from osgeo import ogr
    wkb = qgisgeom.asWkb()
    geom_wkb = ogr.CreateGeometryFromWkb(wkb)
    geom_wkb.GetGeometryType() == ogr.wkbPoint25D
    True
    geom_wkb.GetX(),geom_wkb.GetY(),geom_wkb.GetZ()
    (205552.628666262, 89857.66932836606, 222.1999969482422)
```

So, for create a 3D shapefile in the Python console or in the Python shell you must use modules that are able to handle the z value like ogr, Fiona with Shapely or Pyshp ([CreateElevationValues][2]) 

Example with Fiona:

```python
   
    import fiona
    from shapely.geometry import mapping, Point
    # example of a 3D point (Shapely)
    point = Point(0, 0, 3)
    # schema of the shapefile
    schema = {'geometry': '3D Point','properties': {'id': 'int'},}
    with fiona.collection('my_shp3D.shp', 'w', 'ESRI Shapefile', schema) as output:
       ## If there are multiple geometries, put the "for" loop here
       feat = {}
       feat['geometry'] = mapping(point)
       feat['properties'] = {'id': 1}
       output.write(feat)
```

Control:
```python

    d = fiona.open('my_shp3D.shp')
    d.schema
    {'geometry': '3D Point', 'properties': {u'id': 'int'}}
    d.next()
    {'geometry': {'type': 'Point', 'coordinates': (0.0, 0.0, 3.0)}, 'id': '0', 'properties': {'id': 1}}
```

As an example of extracting the z value from a DEM and the color value from a geological map, see "Python: Using vector and raster layer in a geological perspective without GIS software", [Python: utilisation des couches vectorielles et matricielles dans une perspective géologique, sans logiciel SIG ][3],  in French but the Python scripts are universal...






  [1]: http://osgeo-org.1560.n6.nabble.com/Why-is-it-not-possible-to-extract-the-z-value-of-a-wkb-geometry-with-PyQGIS-while-it-is-possible-wit-td5006814.html
  [2]: http://code.google.com/p/pyshp/wiki/CreateElevationValues
  [3]: http://www.portailsig.org/content/python-utilisation-des-couches-vectorielles-et-matricielles-dans-une-perspective-geologique-
