You must first understand how PyQGIS handles geometry ([Geometry Handling][1])

from [How can I create a line with three points with Python in QGIS?](http://gis.stackexchange.com/questions/60307/how-can-i-create-a-line-with-three-points-with-python-in-qgis/60382)


The most important element is the point:

> QgsPoint(x,y)

and a line or a segment of line are composed of two points:

> QgsGeometry.fromPolyline([QgsPoint(x1,y1),QgsPoint(x2,y2)]));

So to construct a line:

```python
    line_start = QgsPoint(50,50)
    line_end = QgsPoint(100,150)
    line = QgsGeometry.fromPolyline([line_start,line_end])
 ```

and with a memory layer (geometry only, without the attributes):

 
```python
    # create a new memory layer
    v_layer = QgsVectorLayer("LineString", "line", "memory")
    pr = v_layer.dataProvider()
    # create a new feature
    seg = QgsFeature()
    # add the geometry to the feature, 
    seg.setGeometry(QgsGeometry.fromPolyline([line_start, line_end]))
    # ...it was here that you can add attributes, after having defined....
    # add the geometry to the layer
    pr.addFeatures( [ seg ] )
    # update extent of the layer (not necessary)
    v_layer.updateExtents()
    # show the line  
    QgsMapLayerRegistry.instance().addMapLayers([v_layer])
 ```

the result is:

![enter image description here][2]

with 3 points, just add it as a new feature:
 
```python
    newpoint = QgsPoint(143,125)
    v_layer = QgsVectorLayer("LineString", "line_3pt", "memory")
    pr = v_layer.dataProvider()
    seg = QgsFeature()
    seg.setGeometry(QgsGeometry.fromPolyline([line_start, line_end]))
    # new feature: line from line_end to newpoint
    seg = QgsFeature()
    seg.setGeometry(QgsGeometry.fromPolyline([line_end, newpoint]))
    pr.addFeatures( [ seg ] )
    v_layer.updateExtents()
    # add the line to 
    QgsMapLayerRegistry.instance().addMapLayers([v_layer])
 ```

and the result is:

![enter image description here][3]

And with a for loop you can create a line with many segments:

![enter image description here][4]


  [1]: http://www.qgis.org/pyqgis-cookbook/geometry.html
  [2]: http://i.stack.imgur.com/S1VCk.jpg
  [3]: http://i.stack.imgur.com/EpNor.jpg
  [4]: http://i.stack.imgur.com/6Vw0p.jpg
