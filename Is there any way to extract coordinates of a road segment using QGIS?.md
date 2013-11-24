From [Is there any way to extract coordinates of a road segment using QGIS?](http://gis.stackexchange.com/questions/28092/is-there-any-way-to-extract-coordinates-of-a-road-segment-using-qgis)

In fact, it is very easy in the Python console

```python

    def select_all(layer):
        layer.select([])
        layer.setSelectedFeatures([obj.id() for obj in couche])
    
    # selection of the active layer
    mylayer = qgis.utils.iface.activeLayer()
    
    # selection of all the elements (geometry + attributes) of active layer
    select_all(mylayer)

    # coordinates of the vertices/nodes of every feature of a polyline
    for i, feature in enumerate(mylayer.selectedFeatures()):
         geom= feature.geometry()
         xy=geom.asPolyline()
         print "i, xy

    0 [(206643,125181), (201007,121518), (208616,118700), (199035,115037), (200726,111937), (192835,107428), (192835,107428)]
    1 [(196157,123493), (193933,121483), (198411,119320), (195456,117523), (198137,117614)]

    # extraction in wkt format
    for i, feature in enumerate(mylayer.selectedFeatures()):
        geom= feature.geometry()
        wkt = geom.exportToWkt()
        print i, wkt

    0 LINESTRING(206643.215176 125181.180586, 201007.334329 121517.855521, 208615.775876 118699.916872, 199034.777658 115036.590588, 200725.543215 111936.856010, 192835.309877 107428.147942, 192835.309877 107428.147942)
    1 LINESTRING(196156.747710 123492.901991, 193933.267396 121482.632118, 198410.686659 119320.069073, 195456.199118 117523.009641, 198136.558949 117614.385545)
```
