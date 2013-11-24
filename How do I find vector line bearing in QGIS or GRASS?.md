 
 from [How do I find vector line bearing in QGIS or GRASS?](http://gis.stackexchange.com/questions/55449/how-do-i-find-vector-line-bearing-in-qgis-or-grass/55480)
 
 1. With PyQGIS in the Python console, see [How to add Direction and Distance to attribute table?][1] for the azimuths of the segments of a line (with the azimuth functions of Points: **point1.azimuth(point2**))
 2. but you can use many other Python modules like Shapely and Fiona without using QGIS see [Python: traitement des couches vectorielles dans une perspective géologique, lecture et enregistrement des couches sous forme de dictionnaires avec le module Fiona][2] (in French)

```python

        from shapely.geometry import Point, LineString, mapping
        import fiona
        import math

        def azimuth(point1, point2):
           '''azimuth between 2 shapely points (interval 0 - 180°, for my work)'''
           angle = math.atan2(point2.x - point1.x, point2.y - point1.y)
           return math.degrees(angle)if angle>0 else math.degrees(angle) + 180

        def pair(list):
           '''Iterate over pairs in a list '''
            for i in range(1, len(list)):
                yield list[i-1], list[i]

        with fiona.collection('testline.shp', 'r') as input:
            # copy of the input schema'
            schema = input.schema.copy()
            # creation of a new field for storing azimuth
            schema['properties']['azimuth'] = 'int'
            # copy of the original shapefile with the field azimuth to a new shapefile
            with fiona.collection('testline_azim.shp', 'w', 'ESRI Shapefile', schema) as output:
                for line in input:
                # use of pair function to extract the line segments
                    for seg_start, seg_end in pair(line['geometry']['coordinates']):
                       line_start = Point(seg_start)
                       line_end = Point(seg_end)
                       segment = LineString([line_start.coords[0],line_end.coords[0]])
                       elem = {}                  
                       elem['properties'] = line['properties'] 
                       elem['properties']['azimuth'] = azimuth(line_start, line_end)
                       elem['geometry'] = mapping(segment)
                       output.write(elem)
```

Result
     ![from Portail SIG][3]
image from [PortailSIG][4]

**With GRASS:**

 1. split the polyline into individual segments with v.split vertices=1 
 2. upload the azimuth of each segment into the attribute table with v.to.db option=azimuth 

see [Angle between segments of a polyline ][5] or [azimuth of lines with v.to.db? ][6]

**Update** 

in the Python console of QGIS with only PyQGIS

```python

    from PyQt4.QtCore import *
    import math

    def select_all(layer):
        layer.select([])
        layer.setSelectedFeatures([obj.id() for obj in layer])

    def azimuth(point1, point2):
        '''azimuth between 2 QGIS points ->must be adapted to 0-360°'''
        angle = math.atan2(point2.x() - point1.x(), point2.y() - point1.y())
        return math.degrees(angle)

    def pair(list):
        '''Iterate over pairs in a list '''
        for i in range(1, len(list)):
            yield list[i-1], list[i]

    mylayer = qgis.utils.iface.activeLayer()
    # select all the elements of the layer
    select_all(mylayer)

    # Memory layer
    v_layer = QgsVectorLayer("LineString", "azimuth_lines", "memory")
    pr = v_layer.dataProvider()
    pr.addAttributes( [ QgsField("azimuth", QVariant.Int), QgsField("az_pt1pt2",QVariant.Int),QgsField("az_pt2-1",QVariant.Int)])

    for elem in mylayer.selectedFeatures():
        line = elem.geometry().asPolyline()
        for seg_start, seg_end in pair(line):
            line_start = QgsPoint(seg_start)
            line_end = QgsPoint(seg_end)
            seg = QgsFeature()
            seg.addAttribute(0, QVariant(int(azimut(line_start,line_end))))
            # with the functions of PyQGIS
            seg.addAttribute(1, QVariant(int(line_start.azimuth(line_end))))
            seg.addAttribute(2, QVariant(int(line_end.azimuth(line_start))))
            seg.setGeometry(QgsGeometry.fromPolyline([line_start, line_end]))
            pr.addFeatures( [ seg ] )
            v_layer.updateExtents()
            v_layer.updateFieldMap()

    QgsMapLayerRegistry.instance().addMapLayers([v_layer])
```

Result
![enter image description here][7]

and 360 -159 = 201 -180 = 21

  [1]: http://gis.stackexchange.com/questions/24260/how-to-add-direction-and-distance-to-attribute-table/24430#24430
  [2]: http://www.portailsig.org/content/python-traitement-des-couches-vectorielles-dans-une-perspective-geologique-lecture-et-enregi
  [3]: http://i.stack.imgur.com/Xg98Z.jpg
  [4]: http://www.portailsig.org/
  [5]: http://osgeo-org.1560.n6.nabble.com/Angle-between-segments-of-a-polyline-td3895453.html
  [6]: http://osgeo-org.1560.n6.nabble.com/azimuth-of-lines-with-v-to-db-td3915504.html
  [7]: http://i.stack.imgur.com/WC51u.jpg
