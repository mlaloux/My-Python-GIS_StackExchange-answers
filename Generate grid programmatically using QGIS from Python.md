From an existing layer:

![enter image description here][1]

####1) you can use "qgis:creategrid" in Processing as in the "Hex grid from layer bounds" in Scripts/Example scripts. (look at [Processing-Help / qgis / creategrid.rst][2])

rectangular grid
  
```python

    ##[Example scripts]=group
    ##input=vector
    ##cellsize=number 1000.0
    ##grid=output vector
    input = processing.getobject(input)
    centerx = (input.extent().xMinimum() + input.extent().xMaximum()) / 2
    centery = (input.extent().yMinimum() + input.extent().yMaximum()) / 2
    width = (input.extent().xMaximum() - input.extent().xMinimum())
    height = (input.extent().yMaximum() - input.extent().yMinimum())
    processing.runalg("qgis:creategrid", cellsize, cellsize, width, height, centerx, centery, 1, input.crs().authid(), grid)
    
```

![enter image description here][3]

For other types of grids change the value in runalg (1,...,3, ...):

Hexagonal grid

```python

    ....
    processing.runalg("qgis:creategrid", cellsize, cellsize, width, height, centerx, centery, 3, input.crs().authid(), grid)
    
```

![enter image description here][4]

####2) You can adapt the script of [ustroetz][5] in [Create a square grid polygon shapefile with python][6]


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

![enter image description here][7]


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

or use the Python modules  [shapely][8] and [Fiona][9] without QGIS:

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


 [1]: http://i.stack.imgur.com/13iuc.jpg
  [2]: https://github.com/alexbruy/Processing-Help/blob/master/qgis/creategrid.rst
  [3]: http://i.stack.imgur.com/w0Z6F.jpg
  [4]: http://i.stack.imgur.com/SbLuu.jpg
  [5]: http://gis.stackexchange.com/users/15607/ustroetz
  [6]: http://gis.stackexchange.com/questions/54119/create-a-square-grid-polygon-shapefile-with-python/78030#78030
  [7]: http://i.stack.imgur.com/U825j.jpg
  [8]: http://gispython.org/shapely/manual.html
  [9]: http://toblerity.github.com/fiona/manual.html
  
