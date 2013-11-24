From [Get vector features inside a specific extent](http://gis.stackexchange.com/questions/57964/get-vector-features-inside-a-specific-extent)


You don't need a SQL-query to do that, only Python with, once again, the modules [Fiona][1]  and [Shapely][2] of Sean Gillies.

I want only the records which are within the blue frame (analogy of a raster layer).

![enter image description here][3]

See [the Fiona user Manual][4], the filter() method returns an iterator over records that intersect a given (minx, miny, maxx, maxy) bounding box:

```python
    from shapely.geometry import mapping, shape
    import fiona
    # Read the original Shapefile
    input = fiona.open('data.shp', 'r')
    # bounds of the original shapefile
    input.bounds
    (258018.9133083854, 158162.863836, 268763.670357, 162621.686305)
    # clip the shapefile with the raster bounds 
    clipped = input.filter(bbox=((262236.3101588468, 159973.80344954136, 263491.7250217228, 160827.485556297)))
    # create the clipped shapefile with the same schema
    clipped_schema = input.schema.copy()
    with fiona.collection('clipped.shp', 'w', 'ESRI Shapefile', clipped_schema) as output:
        for elem in clipped:
               output.write({'properties': elem['properties'],'geometry': mapping(shape(elem['geometry']))})
```
Result:

![enter image description here][5]




  [1]: https://pypi.python.org/pypi/Fiona/0.10
  [2]: https://pypi.python.org/pypi/Shapely/1.2.17
  [3]: http://i.stack.imgur.com/aXP4g.jpg
  [4]: http://toblerity.github.io/fiona/manual.html#filtering
  [5]: http://i.stack.imgur.com/Ej95G.jpg
