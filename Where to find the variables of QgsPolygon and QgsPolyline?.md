From [Where to find the variables of QgsPolygon and QgsPolyline?](http://gis.stackexchange.com/questions/64247/where-to-find-the-variables-of-qgspolygon-and-qgspolyline)


see [Geometry Handling][1]

 1. a point:   QgsPoint(x,y)
 2. a line:    QgsGeometry.fromPolyline([QgsPoint(x1,y1),QgsPoint(x2,y2)]))
 3. a polygon: QgsGeometry.fromPolygon([[**QgsPoint(x1,y1)**,QgsPoint(x2,y2), QgsPoint(x3,y3),**QgsPoint(x1,y1)**]]) -> the polygon must be closed.

thus:

```python
    line_start= QgsPoint(50,50)
    line_end= QgsPoint(100,100)
    line = QgsGeometry.fromPolyline([line_start, line_end])
    points = [QgsPoint(60,60),QgsPoint(60,80),QgsPoint(80,80),QgsPoint(80,60),QgsPoint(60,60)] 
    polygon= QgsGeometry.fromPolygon([points])
```

and:

```python

    feature.setGeometry(QgsGeometry.fromPolyline([line_start, line_end]))
    feature.setGeometry(QgsGeometry.fromPolygon([points]))
```


  [1]: http://www.qgis.org/pyqgis-cookbook/geometry.html
