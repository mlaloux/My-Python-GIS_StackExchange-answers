from [How to create points based on the distance and bearing from a survey point?](http://gis.stackexchange.com/questions/77974/how-to-convert-gml-to-geojson-using-python-and-ogr-with-geometry-transformation/77999)


It is pure trigonometry or vector calculus problem and you can get the result using polar coordinates (center of the figure) or the direction cosines (right) with 2D cartesian coordinates :

![enter image description here][1]

#with polar coordinates in Python

from a point = QgsPoint(-1004.00,635.00), a distance of 160m and a bearing of 103°

```python
    # bearing in radians
    bearing = math.radians(bearing)
    dist_x, dist_y = (distance * math.sin(angle),distance * math.cos(angle))
    print dist_x, dist_y
    (155.89921036563763, -35.992168695018407)
    xfinal, yfinal = (point.x() + dist_x, point.y() + dist_y)
    print xfinal, yfinal
    (-848.1007896343624, 599.00783130498155)
    # resulting point
    point_res= QgsPoint(xfinal,yfinal)
```

#with direction cosines in Python

```python
    # bearing in radians
    bearing = math.radians(bearing)
    # direction cosines
    cosa = math.sin(bearing)
    cosb = math.cos(bearing)
    xfinal, yfinal = (point.x() +(distance*cosa), point.y()+(distance*cosb))
    print xfinal,yfinal
    (-848.1007896343624, 599.00783130498155)
```

Result

![enter image description here][2]

see also [How to create points in a specified distance along the line in QGIS？][3]


  [1]: http://i.stack.imgur.com/Zo2Do.jpg
  [2]: http://i.stack.imgur.com/Nb0jK.jpg
  [3]: http://gis.stackexchange.com/questions/63201/how-to-create-points-in-a-specified-distance-along-the-line-in-qgis/63238#63238
