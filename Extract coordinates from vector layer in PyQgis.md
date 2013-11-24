From [Extract coordinates from vector layer in PyQgis](http://gis.stackexchange.com/questions/53594/extract-coordinates-from-vector-layer-in-pyqgis)

Everything can be simplified in a more Pythonic way (see in [How to add Direction and Distance to attribute table?][1] ):

1) function to select all the elements (geometry and attributes) of a layer:

```python
    def select_all(layer):
       layer.select([])
       layer.setSelectedFeatures([obj.id() for obj in layer])
```
2) selection of the layer

```python
    mylayer = qgis.utils.iface.activeLayer()
    select_all(mylayer)
```
3) processing the layer

```python
    for elem in mylayer.selectedFeatures():
         geom= elem.geometry()
         attrs = elem.attributeMap()
         
         # example with geometry
         wkt = geom.exportToWkt()
         print wkt

         # example with attributes
         for (k,atr) in attrs.iteritems():
                print "%d: %s" % (k, atr.toString())
```
example of results:

for geometry:

LINESTRING(110923.171250 113663.674220, 117364.375933 120736.374336, 117364.375933 120736.374336)

LINESTRING(112501.896619 119157.645479, 118248.464701 116189.640235)...

for attributes:

0, -100, test

1, -200, test2

...

If you want an iterator:

```python
    for i, elem in enumerate(mylayer.selectedFeatures()):
         geom= elem.geometry()
         wkt = geom.exportToWkt()
         print "element: ", i, "wkt: ", wkt

element:  0 wkt:  LINESTRING(110923.171250 113663.674220, 117364.375933 120736.374336, 117364.375933 120736.374336)
element:  1 wkt:  LINESTRING(112501.896619 119157.645479, 118248.464701 116189.640235)
```python

and for the extraction of the xy coordinates, see [How to add Direction and Distance to attribute table?][2] 



  [1]: http://gis.stackexchange.com/questions/24260/how-to-add-direction-and-distance-to-attribute-table
  [2]: http://gis.stackexchange.com/questions/24260/how-to-add-direction-and-distance-to-attribute-table
  
