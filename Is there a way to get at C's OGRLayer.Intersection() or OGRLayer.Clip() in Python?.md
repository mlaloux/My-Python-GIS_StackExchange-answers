from [Is there a way to get at C's OGRLayer.Intersection() or OGRLayer.Clip() in Python?](http://gis.stackexchange.com/questions/33370/is-there-a-way-to-get-at-cs-ogrlayer-intersection-or-ogrlayer-clip-in-pytho/33428)

A layer is composed of one or several geometries. For the intersection of layers, you must iterate through each layer geometries. With shapely it is easy, example with two shapefiles:

```python
    from osgeo import ogr
    from shapely.wkb import loads
    from shapely.geometry import *
    # first layer, a polygon shapefile
    first = Polygon()
    # open shapefile
    source1 = ogr.Open("test1.shp")
    layer1 = source1.GetLayer()
    # combination of all the geometries of the layer in a single shapely object
    for element in layer1:
       geom = loads(element.GetGeometryRef().ExportToWkb())
       first = first.union(geom)    
    # second layer, a polygon shapefile
    two = Polygon()
    source2 = ogr.Open("test2.shp")
    layer2 = source2.GetLayer()
    for element in layer2:
       geom = loads(element.GetGeometryRef().ExportToWkb())
       two = two.union(geom)

    # intersection between the two layers 
    print first.intersection(two).wkt
```

It is possible to use the same type of treatment for Clip(). Another solution is provided by [Creating a little clipbox for your GIS projects in Python][1]


  [1]: http://blog.technokrat.nl/?p=369
