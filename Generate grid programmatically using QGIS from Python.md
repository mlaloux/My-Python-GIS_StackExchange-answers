You can adapt the script of [ustroetz][1] in [Create a square grid polygon shapefile with python][2]

part of my class to create a memory layer limited here to the geometry of Polygons:

```python
    class Crea_layer(object):
        def __init__(self,name,type):
            self.type=type
            self.name = name
            self.layer =  QgsVectorLayer(self.type, self.name , "memory")
            self.pr =self.layer.dataProvider() 
        def create_poly(self,points):
            self.seg = QgsFeature()  
            self.seg.setGeometry(QgsGeometry.fromPolygon([points]))
            self.pr.addFeatures( [self.seg] )
            self.layer.updateExtents()
        @property
        def disp_layer(self):
            QgsMapLayerRegistry.instance().addMapLayers([self.layer])
```

The script to create a squared grid layer from an existing layer:

![enter image description here][3]

```python

    from math import ceil
    canvas= qgis.utils.iface.mapCanvas()
    # first layer
    layer = canvas.layer(0)
    xmin,ymin,xmax,ymax = layer.extent().toRectF().getCoords()
    gridWidth = 1000
    gridHeight = 1000
    rows = ceil((ymax-ymin)/gridHeight)
    cols = ceil((xmax-xmin)/gridWidth)
    ringXleftOrigin = xmin
    ringXrightOrigin = xmin + gridWidth
    ringYtopOrigin = ymax
    ringYbottomOrigin = ymax-gridHeight
    pol = Crea_layer("grid", "Polygon")
    for i in range(int(cols)):
        # reset envelope for rows
        ringYtop = ringYtopOrigin
        ringYbottom =ringYbottomOrigin
        for j in range(int(rows)):
            poly = [QgsPoint(ringXleftOrigin, ringYtop),QgsPoint(ringXrightOrigin, ringYtop),QgsPoint(ringXrightOrigin, ringYbottom),QgsPoint(ringXleftOrigin, ringYbottom),QgsPoint(ringXleftOrigin, ringYtop)] 
            pol.create_poly(poly) 
            ringYtop = ringYtop - gridHeight
            ringYbottom = ringYbottom - gridHeight
        ringXleftOrigin = ringXleftOrigin + gridWidth
        ringXrightOrigin = ringXrightOrigin + gridWidth

    pol.disp_layer
    
```

     
Result:

![enter image description here][4]

But you can modify the script using numpy in place of math ceil and int

```python

    import numpy as np
    rows = (ymax-ymin)/gridHeight
    cols = (xmax-xmin)/gridWidth
    for i in np.arange(cols):
        ....
        for j in np.arange(rows):
             ....
```
and if you don't understand xmin,ymin,xmax,ymax = layer.extent().toRectF().getCoords()


```python

    xmin = layer.extent().xMinimum()
    xmax = layer.extent().xMaximum()
    ymin = layer.extent().yMinimum()
    ymax = layer.extent().yMaximum()
    # = in one line
    xmin,ymin,xmax,ymax = layer.extent().toRectF().getCoords()
```

and to go faster, you can even use the gridding functions of numpy 

### no published

or use the Python modules  [shapely][5] and [Fiona][6] without QGIS:

```python

    from shapely.geometry import Polygon, mapping
    import numpy as np
    import fiona
    xmin,ymin,xmax,ymax = fiona.open("tourshapefile.shp").bounds
    gridWidth = 1000
    gridHeight = 1000
    rows = (ymax-ymin)/gridHeight
    cols = (xmax-xmin)/gridWidth
    ringXleftOrigin = xmin
    ringXrightOrigin = xmin + gridWidth
    ringYtopOrigin = ymax
    ringYbottomOrigin = ymax-gridHeight
    schema = {'geometry': 'Polygon','properties': {'test': 'int'}}
    with fiona.open('grid.shp','w','ESRI Shapefile', schema) as c:
        for i in np.arange(cols):
            ringYtop = ringYtopOrigin
            ringYbottom =ringYbottomOrigin
            for j in np.arange(rows):
                polygon = Polygon([(ringXleftOrigin, ringYtop), (ringXrightOrigin, ringYtop), (ringXrightOrigin, ringYbottom), (ringXleftOrigin, ringYbottom)])
                c.write({'geometry':mapping(polygon), 'properties':{'test':1}})
                ringYtop = ringYtop - gridHeight
                ringYbottom = ringYbottom - gridHeight
            ringXleftOrigin = ringXleftOrigin + gridWidth
            ringXrightOrigin = ringXrightOrigin + gridWidth

 ```


  [1]: http://gis.stackexchange.com/users/15607/ustroetz
  [2]: http://gis.stackexchange.com/questions/54119/create-a-square-grid-polygon-shapefile-with-python/78030#78030
  [3]: http://i.stack.imgur.com/13iuc.jpg
  [4]: http://i.stack.imgur.com/3Ei8L.jpg
  [5]: http://gispython.org/shapely/manual.html
  [6]: http://toblerity.github.com/fiona/manual
