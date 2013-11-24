from [Is it possible to route shapefiles using python and without ArcGIS, QGIS, or Pgrouting?](http://gis.stackexchange.com/questions/65056/is-it-possible-to-route-shapefiles-using-python-and-without-arcgis-qgis-or-pgr/65087)

The link given by MappaGnosis is the first attempt to implement Graph theory algorithms in Python (by Guido van Rossum, the creator of Python).

Since, many modules were developed:

 - [Graph theory][1]
 - [network][2]
 - [routing network][3]

One of the most comprehensive is [NetworkX][4], mentioned before in GS

 - it can read or write shapefiles natively (thanks to bwreilly in [nx_spatial][5])
 - all the graph algorithms are implemented ([Graph traversal][6], [Shortests Paths][7] with the A* algorithm and many more)

```python

    import networkx  as nx  
    graph = nx.read_shp('lines.shp')  
    print graph.nodes()  
    [(1.0, 2.0), (3.0, 2.0),...]  
    print graph.edges()
    [((1.0, 2.0), (1.0, 1.0)),...]
```

Result with matplotlib  
![enter image description here][8]

Result with graphviz:  
![enter image description here][9]

A* Algorithm

```python

    def dist(a, b):
       (x1, y1) = a
       (x2, y2) = b
        return ((x1 - x2) ** 2 + (y1 - y2) ** 2) ** 0.5

    print(nx.astar_path(graph,(3.0,2.0),(1.0, 1.0),dist))
    [(3.0, 2.0), (2.0, 1.0), (1.0, 1.0)]
```

and you can export the results:

to shapefiles:

```python

    nx.write_shp(graph, ‘/shapefiles’)
```

to ogr geometries:

```python
    from osgeo import ogr
    line = osgeo.ogr.Geometry(ogr.wkbLineString)
    from points in (nx.astar_path(graph,(3.0,2.0),(1.0, 1.0),dist)):
        line.AddPoint(points[0],points[1])

    print line.ExportToWkt()
    LINESTRING (3 2 0,2 1 0,1 1 0)
```

or to [shapely][10] geometries:

```python

    from shapely.geometry import LineString
    line = LineString(nx.astar_path(graph,(3.0,2.0),(1.0, 1.0),dist))
    print line.wkt
    LINESTRING (3.00 2.00, 2.00 1.00, 1.00 1.00)
```

![enter image description here][11]

    


  [1]: http://wiki.python.org/moin/PythonGraphApi
  [2]: https://pypi.python.org/pypi?:action=search&term=network&submit=search
  [3]: https://pypi.python.org/pypi?:action=search&term=routing%20network&submit=search
  [4]: http://networkx.github.io/documentation/latest/reference/index.html
  [5]: https://bitbucket.org/gallipoli/nx_spatial/wiki/Home
  [6]: http://networkx.lanl.gov/reference/algorithms.traversal.html
  [7]: http://networkx.lanl.gov/reference/algorithms.shortest_paths.html
  [8]: http://i.stack.imgur.com/3dVYG.png
  [9]: http://i.stack.imgur.com/gEvg4.png
  [10]: http://toblerity.github.io/shapely/manual.html
  [11]: http://i.stack.imgur.com/um8uh.png
