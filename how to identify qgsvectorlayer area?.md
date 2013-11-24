From [how to identify qgsvectorlayer area?](http://gis.stackexchange.com/questions/76193/how-to-identify-qgsvectorlayer-area)

A Polygon vectorlayer is composed of many features (geometries), each with an area.

If the geometries don't overlap, you can iterate over the vector layer and sum the areas:

```python
    layer = iface.activeLayer()
    areatot = 0
    for elem in layer.getFeatures()
         geom = elem.geometry()
         areatot += geom.area()
```
or in one line:
```python
    aeratot = [sum(elem.geometry().area() for elem in layer.getFeatures())]
```
But if some geometries overlap, this result is wrong:  

![enter image description here][1]

You can then Union all the geometries and use the area of the resulting geometry:

![enter image description here][2]

```python
    # creation of a empty geometry for unioning
    geomtot = QgsGeometry.fromWkt('GEOMETRYCOLLECTION EMPTY')
    # union
    for elem in layer.getFeatures():
       geomtot = geomtot.combine(elem.geometry())
    area = geomtot.area()
```
For QGIS 1.8:
----

```python
    layer = qgis.utils.iface.activeLayer()
    areatot = 0
    layer.select()
    for elem in layer:
         geom = elem.geometry()
         areatot += geom.area()
```
and: 

```python
    geomtot = QgsGeometry.fromWkt('GEOMETRYCOLLECTION EMPTY')
    layer.select()
    for elem in layer:
        geomtot = geomtot.combine(elem.geometry())
    area = geomtot.area()
```
        

  [1]: http://i.stack.imgur.com/bEPLc.jpg
  [2]: http://i.stack.imgur.com/evMPJ.jpg
