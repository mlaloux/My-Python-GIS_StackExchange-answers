From [Python: How to create equidistant points in QGIS along a polygon?](gis.stackexchange.com/questions/81481/how-to-create-equidistant-points-in-qgis-along-a-polygon)

You cannot use interpolate() with polygons:

If you have a polygon:

```python
    geom.exportToGeoJSON()
    u'{ "type": "Polygon", "coordinates": [ [ [167546.39129160347511061, 156935.60758662305306643], [167464.55340823522419669, 154998.77768024150282145], [169183.148958968144143, 154698.70544122465071268], [167901.02211953248479404, 153498.41648515724227764], [167109.92258030621451326, 152734.59624038706533611], [165364.04773511723033153, 154725.98473568074405193], [167546.39129160347511061, 156935.60758662305306643] ] ] }'  
    geom.asPolygon()
    [[(167546,156936), (167465,154999), (169183,154699), (167901,153498), (167110,152735), (165364,154726), (167546,156936)]]
    # interpolate
    geom.interpolate(100).asPoint()
    Traceback (most recent call last):
    File "<input>", line 1, in <module>
    AttributeError: 'NoneType' object has no attribute 'asPoint'
```

The LinearRing of the Polygon is:

```python

    LinearRing = QgsGeometry.fromPolyline(geom.asPolygon()[0])
    LinearRing.exportToGeoJSON()
    u'{ "type": "LineString", "coordinates": [ [167546.39129160347511061, 156935.60758662305306643], [167464.55340823522419669, 154998.77768024150282145], [169183.148958968144143, 154698.70544122465071268], [167901.02211953248479404, 153498.41648515724227764], [167109.92258030621451326, 152734.59624038706533611], [165364.04773511723033153, 154725.98473568074405193], [167546.39129160347511061, 156935.60758662305306643] ] }'
    # interpolate
    LinearRing.interpolate(100).asPoint()
    (167542,156836)
```   

Polygons:

![enter image description here][1]


Part of a class to create memory layers from the Python console:

```python

    class Crea_layer(object):
       def __init__(self,nom,type):
           self.type=type
           self.name = nom
           self.layer =  QgsVectorLayer(self.type, self.name , "memory")
           self.pr =self.layer.dataProvider() 
      def create_point(self,geometry):
           self.seg = QgsFeature()
           self.seg.setGeometry(QgsGeometry.fromPoint(geometry))
           self.pr.addFeatures([self.seg])
           self.layer.updateExtents()
      @property
      def show_layer(self):
           QgsMapLayerRegistry.instance().addMapLayers([self.layer])
           
```

Creation of the equidistant points (step = 100m)

```python

    polys = qgis.utils.iface.activeLayer()
    points = Crea_layer("equidistant", "Point")
    for feat in polys.getFeatures():
        geom = feat.geometry()
        line = QgsGeometry.fromPolyline(geom.asPolygon()[0])
        for distance in xrange(0,line.length(),100):
             point = line.interpolate(distance)
             point = point.asPoint()
             point.create_point(QgsPoint(point.x(),point.y()))

    points.show_layer
```
Result:

![enter image description here][2]



 [1]: http://i.stack.imgur.com/4Umdz.jpg
 [2]: http://i.stack.imgur.com/dCvZu.jpg
