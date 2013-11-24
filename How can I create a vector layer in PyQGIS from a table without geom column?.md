From [How can I create a vector layer in PyQGIS from a table without geom column?](http://gis.stackexchange.com/questions/70962/how-can-i-create-a-vector-layer-in-pyqgis-from-a-table-without-geom-column)

Nothing

With a SQLite layer (not spatial)

```python

    uri = QgsDataSourceURI()
    uri.setDatabase('Midv_31370.sqlite')
    uri.setDataSource('', 'obs_points','','','obsid')
    vlayer = QgsVectorLayer(uri.uri(), 'TestLayer2', 'spatialite')
    QgsMapLayerRegistry.instance().addMapLayer(vlayer)
    vlayer.isValid()
    True
```
![enter image description here][1]

If you want a spatial layer (Spatialite), you must indicate the geometry column:

```python

    uri = QgsDataSourceURI()
    uri.setDatabase('Midv_31370.sqlite')
    uri.setDataSource('', 'obs_points', 'geometry')
    vlayer = QgsVectorLayer(uri.uri(), 'TestLayer2', 'spatialite')
    QgsMapLayerRegistry.instance().addMapLayer(vlayer)
    vlayer.isValid()
    True
```python

![enter image description here][3]


  [1]: http://i.stack.imgur.com/8TzYE.jpg
  [2]: http://i.stack.imgur.com/USt5h.jpg
  [3]: http://i.stack.imgur.com/PY6GM.jpg
