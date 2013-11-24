
From [How to write an equivalent to MapBasic's CreateLine in pyQGIS](http://gis.stackexchange.com/questions/56932/how-to-write-an-equivalent-to-mapbasics-createline-in-pyqgis)

You can even use the original coordinates of the point shapefile

```python
 

     # iterator, iterate over pairs of points in a list, a layer
    `def pair_points(iterable):
         iterator = iter(iterable)
         prev = None
         item = iterator.next() 
         for next in iterator:
             yield (item,next)
             prev = item
             item = next

      `# using the iterator 
      `for pt1,pt2 in pair_points(layer.getFeatures()):
           ptA = pt1.geometry().asPoint()
           ptB = pt2.geometry().asPoint()
           print ptA, ptB, 
           .....
           line = QgsGeometry.fromPolyline([QgsPoint(ptA), QgsPoint(ptB)])
           print line.asPolyline()
           .....
```
Example with one of my points layers
```python

    (271927,155249) (272361,153856) line: [(271927,155249), (272361,153856)]
    (272361,153856) (272689,152802) line: [(272361,153856), (272689,152802)]
    ....
```

     
