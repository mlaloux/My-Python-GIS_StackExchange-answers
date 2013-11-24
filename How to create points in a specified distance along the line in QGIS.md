from [How to create points in a specified distance along the line in QGIS？](http://gis.stackexchange.com/questions/63201/how-to-create-points-in-a-specified-distance-along-the-line-in-qgis/63238)

In the Python console:

 - you can use the  [Shapely][1] module (as in [How to create equidistant points in QGIS?][2]) with the function 
 
```python
    point = line.interpolate(currentdistance)
```

 - The new Python API of the master version (1.9) has an equivalent command (as in [Generating chainage (distance) nodes in QGIS ][3] of Nathan Woodrow)

```python
    point = geom.interpolate(currentdistance)
```

 - or you can use vector algebra and direction cosines (as in [PyQGIS: geometries, vectors, vector algebra or direction cosines, application examples][4], in French)

1) create a memory layer class (complete class (without attributes) at [crea_mem_layer.py][5])

```python
    class Create_vlayer(object):
        '''creation of a virtual layer''' 
         def __init__(self,nom,type):
             self.type=type
             self.name = nom
             self.layer =  QgsVectorLayer(self.type, self.name , "memory")
             self.pr =self.layer.dataProvider() 
         def create_point(self,geometry):
             # add point to the layer
             self.seg = QgsFeature()
             self.seg.setGeometry(QgsGeometry.fromPoint(geometry))
             self.pr.addFeatures([self.seg])
             self.layer.updateExtents()
         @property
         def display_layer(self):
             #end of layer and display layer 
             QgsMapLayerRegistry.instance().addMapLayers([self.couche])
```

2) vector algebra functions (from [algèbre_vect_PyQGIS.py][6])

```python
    import math 
    def mag(point):
        # magnitude of a vector
        return math.sqrt(point.x()**2 + point.y()**2)
    def diff(point2, point1):
        # substraction betwen two vector
        return QgsPoint(point2.x()-point1.x(), point2.y() - point1.y())
    def length(point1,point2):
        # with PyQGIS: sqrDist
        return math.sqrt(point1.sqrDist(point2))
```

3) direction cosines

```python
    def dircos(point):
        cosa = point.x() / mag(point)
        cosb = point.y()/ mag(point)
        return cosa,cosb
```

4) process line or line segments

```python
    def pairs(list):
        # list pairs iteration 
        for i in range(1, len(list)):
        yield list[i-1], list[i]

    layer = qgis.utils.iface.activeLayer()
    # interval between points
    interval = 5 m
    # create virtual layer
    gen_pt  = Create_vlayer("mid5", "Point")

    for elem in layer():
        line = elem.geometry()
        for seg_start, seg_end in pairs(line.asPolyline()):
           line_start = QgsPoint(seg_start)
           line_end = QgsPoint(seg_end)
           # mid point = vector coordinates [x2-x1,y2-y1]
           pointm =diff(line_end, line_start)
           # direction cosines of the segment
           cosa,cosb = dircos(pointm)
           # length of the segment
           lg = length(line_end, line_start)
           # generate and add points to the virtual layer 
           for i in range(interval,lg,interval):
               gen_pt.create_point(QgsPoint(line_start.x()  + (i * cosa), line_start.y() + (i*cosb)))

    # display layer
    gen_pt.display_layer
```python

Results

polyline, equidistant point with Shapely or PyQGIS2, with direction cosines

![polyline][7]![shapely][8]![vector_algebra][9]

Then just adjust the interval


  [1]: https://pypi.python.org/pypi/Shapely/1.2.17
  [2]: http://gis.stackexchange.com/questions/27102/how-to-create-equidistant-points-in-qgis
  [3]: http://nathanw.net/2012/08/05/generating-chainage-distance-nodes-in-qgis/
  [4]: http://www.portailsig.org/content/pyqgis-des-geometries-des-vecteurs-de-l-algebre-vectorielle-ou-des-cosinus-directeurs-exempl
  [5]: http://www.portailsig.org/content/creamemlayerpy
  [6]: http://www.portailsig.org/content/algebrevectpyqgispy
  [7]: http://i.stack.imgur.com/4eVCT.jpg
  [8]: http://i.stack.imgur.com/Exju5.jpg
  [9]: http://i.stack.imgur.com/drkqP.jpg
