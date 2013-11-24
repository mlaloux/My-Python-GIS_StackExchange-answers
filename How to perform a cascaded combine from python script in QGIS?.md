From [How to perform a cascaded combine from python script in QGIS?](http://gis.stackexchange.com/questions/63921/how-to-perform-a-cascaded-combine-from-python-script-in-qgis)

[Shapely][1] is one of the Geos Python bindings and has [cascaded_union][2] and [unary_union][3] implemented since versions 2.16 (GEOSCascadedUnion is deprecated since GEOS version 3.2.+ and GEOSUnaryUnion must be used instead: it can operate on different geometry types, not only polygons as is the case for the older cascaded unions).

Convert QGIS geometries to Shapely geometries in the Python console is very easy:

```python
    from shapely.wkb import loads
    from shapely.ops import cascaded_union, unary_union
    # transform QGIS geometries to a list of shapely geometries
    geoms =[loads(feature.geometry().asWkb()) for feature in layer]
    # cascaded union
    cu= cascaded_union(geoms)
    cu
    <shapely.geometry.polygon.Polygon object at 0x1257d9750>
    # unary union
    un = unary_union(geoms)
    un
    <shapely.geometry.polygon.Polygon object at 0x1257d9c90>
    # convert to Qgis geometry
    geom = QgsGeometry.fromWkt(un.wkt)
    geom
    <qgis.core.QgsGeometry object at 0x1247c7dd0>
```

You can even do it without QGIS with modules like [Fiona][4] to read and write a Shapefile layer.


  [1]: https://pypi.python.org/pypi/Shapely
  [2]: http://toblerity.github.io/shapely/manual.html#shapely.ops.cascaded_union
  [3]: http://toblerity.github.io/shapely/manual.html#shapely.ops.unary_union
  [4]: https://pypi.python.org/pypi/Fiona
