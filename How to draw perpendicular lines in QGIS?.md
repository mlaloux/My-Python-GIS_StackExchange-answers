from [How to draw perpendicular lines in QGIS?](http://gis.stackexchange.com/questions/59169/how-to-draw-perpendicular-lines-in-qgis/59196)



It is a problem of analytical geometry and the solution was given by Paul Bourke in 1998 ([Minimum Distance betweena Point and a Line][1]).  The shortest distance from a point to a line or line segment is the perpendicular from this point to the line segment. Several versions of his algorithm have been proposed in various languages ​​including Python as in [Measuring distance from a point to a line segment in Python.][2] but there are many others (like  [Nearest neighbor between a point layer and a line layer][3] with Shapely)

```python
    # basic example with PyQGIS
    # the end points of the line
    line_start = QgsPoint(50,50)
    line_end = QgsPoint(100,150)
    # the line
    line = QgsGeometry.fromPolyline([line_start,line_end])
    # the point
    point = QgsPoint(30,120)
```

![pt line][4]

```python
    def intersect_point_to_line(point, line_start, line_end):
         ''' Calc minimum distance from a point and a line segment and intersection'''
          # sqrDist of the line (PyQGIS function = magnitude (length) of a line **2)
          magnitude2 = line_start.sqrDist(line_end) 
          # minimum distance
          u = ((point.x() - line_start.x()) * (line_end.x() - line_start.x()) + (point.y() - line_start.y()) * (line_end.y() - line_start.y()))/(magnitude2)
          # intersection point on the line
          ix = line_start.x() + u * (line_end.x() - line_start.x())
          iy = line_start.y() + u * (line_end.y() - line_start.y())
          return QgsPoint(ix,iy)
    
    line = QgsGeometry.fromPolyline([point,intersect_point_to_line(point, line_start, line_end)])
```

and the result is

![result][5]

Adapting the solution to your problem is easy,just loop through all line segments, extracting the segments end points and apply the function.


  [1]: http://paulbourke.net/geometry/pointlineplane/
  [2]: http://nodedangles.wordpress.com/2010/05/16/measuring-distance-from-a-point-to-a-line-segment/
  [3]: http://gis.stackexchange.com/questions/396/nearest-neighbor-between-a-point-layer-and-a-line-layer
  [4]: http://i.stack.imgur.com/xwYK9.jpg
  [5]: http://i.stack.imgur.com/onESj.jpg
