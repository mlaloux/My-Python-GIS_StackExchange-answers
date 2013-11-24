from [How to find the middle point of a line in QGIS](http://gis.stackexchange.com/questions/58079/how-to-find-the-middle-point-of-a-line-in-qgis)


It is a pure geometry problem that can be solved in the Python console

The problem:

![test][1]

Find the midpoint of a segment x1,y1,x2,y2 is easy 

 
 ```python

    x = (x1 + x2)/2
    y = (y1 + y2)/2
 
 ```

so in the Python console 

 
 ```python

    def mid(pt1, pt2):
       x = (pt1.x() + pt2.x())/2
       y = (pt1.y() + pt2.y())/2
       return QgsPoint(x,y)

    def pair(list):
       '''Iterate over pairs in a list '''
        for i in range(1, len(list)):
            yield list[i-1], list[i]

    def create_geometry(point,pr):
        # create geometry record 
        seg = QgsFeature()
        seg.setGeometry(QgsGeometry.fromPoint(point))
        pr.addFeatures( [seg] )

    # memory layer
    pt_layer = QgsVectorLayer("Point", "midpoint", "memory")
    pr = pt_layer.dataProvider()

    for elem in mylayer.selectedFeatures():
        line = elem.geometry()
        for seg_start, seg_end in pair(line.asPolyline()):
           line_start = QgsPoint(seg_start)
           line_end = QgsPoint(seg_end)
           # midpoint 
           midpt= mid(line_start, line_end)
           # add midpoint point to layer
           create_geometry(midpt,pr)
           pt_layer.updateExtents()

    QgsMapLayerRegistry.instance().addMapLayers([pt_layer])
 
 ```

Result

![final][2]


 


  [1]: http://i.stack.imgur.com/PP1Sr.jpg
  [2]: http://i.stack.imgur.com/r1HgU.jpg
