From [geoprocessing across multiple vector layers QGIS2](http://gis.stackexchange.com/questions/77974/how-to-convert-gml-to-geojson-using-python-and-ogr-with-geometry-transformation/77999)



It is possible in Python, pure Python without QGIS with modules such as [Fiona][1], [Pyshp][2], [shapely][3] with [rtree][4] (see [rtree python polygon index][5]) and with PyQGIS. 

###The problem: 6 points layers and an unique grid:

![enter image description here][6]

###A possible solution with PyQGIS:

1) Using a spatial index for the grid (see Using Spatial Index in [Using Vector Layers][7], [Using a QGIS spatial index to speed up your code ][8] or [How to do a spatial search without select() using PyQGIS?][9]):


```python
    grid = iface.activeLayer()
    index = QgsSpatialIndex() 
    for elem in grid.getFeatures():
         index.insertFeature(elem)
```

or more "Pythonic"

```python
    index = QgsSpatialIndex() 
    map(index.insertFeature, grid.getFeatures())
```


2) Creation of a dictionary to collect the results (index of the grid squares where there are points inside and layers) and a function to complete it:

```python
    results = {}
    # fonction to find the indexes of the grid squares inside which a point lies.
    def mydict(layer, dictionary):
        for elem in layer.getFeatures():
            geom = elem.geometry().asPoint()
            nearestIds = index.nearestNeighbor(geom,1)
            results.setdefault(str(nearestIds[0]), set()).add(layer.name())
```

3) Iteration over point layers and application of the function:

```python
    canvas= qgis.utils.iface.mapCanvas()
    for layer in canvas.layers():
        elem = layer.getFeatures().next()
        geom = elem.geometry()
        # only points layers
        if geom.wkbType() == QGis.WKBPoint:
             mydict(layer, results)
```

The result is a dictionary with the grid squares spatial index as key and the layers which have points in the square grid as values:

```python
    print results
    {'11': set([u'point1', u'point3', u'point4']),
     '10': set([u'point5']),
     '13': set([u'point4']), 
     '12': set([u'point3', u'point5', u'point6']),
     '15': set([u'point4', u'point6']), 
     '14': set([u'point1', u'point2', u'point3', u'point4', u'point5', u'point6']),
      '1': set([u'point4', u'point5', u'point6']), 
      '0': set([u'point1', u'point2', u'point3', u'point4', u'point5', u'point6']), 
      '3': set([u'point4', u'point5']), 
      '2': set([u'point3', u'point6']), 
      '5': set([u'point4']),
      '4': set([u'point3', u'point5']),
      '7': set([u'point3', u'point6']), 
      '6': set([u'point1', u'point3']), 
      '9': set([u'point3']), 
      '8': set([u'point1', u'point2'])}
```

If you want to preserve the order of the indexes, you can use an [OrderedDict][10]

4) And if you only want the grid squares with six years of data:

```python

    for index_gridsquare, layers in results.iteritems():
        if len(values) == 6:
             print index_gridsquare,": ", values
    14: set([u'point1', u'point2', u'point3', u'point4', u'point5', u'point6'])
    0 : set([u'point1', u'point2', u'point3', u'point4', u'point5', u'point6'])
```


##After that, I do not understand if you want to select:

 - the square grids:

![enter image description here][11]

 - the points of the six layers present in the square grids:

![enter image description here][12]


  [1]: http://toblerity.org/fiona/manual.html
  [2]: http://code.google.com/p/pyshp/
  [3]: http://toblerity.org/shapely/manual.html
  [4]: http://toblerity.org/rtree/tutorial.html
  [5]: http://gis.stackexchange.com/questions/42931/rtree-python-polygon-index
  [6]: http://i.stack.imgur.com/5mrur.jpg
  [7]: http://www.qgis.org/fr/docs/pyqgis_developer_cookbook/vector.html
  [8]: http://nathanw.net/2013/01/04/using-a-qgis-spatial-index-to-speed-up-your-code/
  [9]: http://gis.stackexchange.com/questions/36887/how-to-do-a-spatial-search-without-select-using-pyqgis
  [10]: http://docs.python.org/2/library/collections.html#collections.OrderedDict
  [11]: http://i.stack.imgur.com/70gx2.jpg
  [12]: http://i.stack.imgur.com/xs7Cb.jpg
