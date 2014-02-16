From [How to count the number of lines connected to a point?](http://gis.stackexchange.com/questions/86613/how-to-count-the-number-of-lines-connected-to-a-point)

It is not a problem of geometry but a simple problem of Graph Theory (nodes and connected edges) and Python has many modules for dealing with Graphs ([PythonGraphApi][1], [Graphs in Python][2], [Python Patterns - Implementing Graphs][3], ...)

In your problem, you don't need a second point layer, you must use the nodes of the polylines. They are the vertex/nodes of a Graph and the segments between two nodes are the edges of the same Graph. And you want to find the nodes with a [degree][4] 2 and 3 (number of edges incident to the vertex = when 2 lines connect to a point, I want to install an elbow, when 3 lines connect, I want to install a Tee)

The layer:

![enter image description here][5]


The equivalent Graph where we can see directly the desired points:

![enter image description here][6]


I will use here the [NetworkX][7] module (pure Python module). The module can directly open shapefiles to generates a networkx.Graph ([Networkx: GIS shapefile][8]) but I do so in the Python console of QGIS:

We need to extract all the segments of the layer (from point to point): the LineString must be iterated over pairs to divide the polyline in segments. 

```Python
    def pairs(list):
    '''generator to iterate over pairs in a list '''
    for i in range(1, len(list)):
        yield list[i-1], list[i]

    import networkx as nx # import the networkx module
    # select the polyline layer
    layer = qgis.utils.iface.activeLayer()
    # create a Graph
    G = nx.Graph()
    # add the nodes and the edges to the Graph = segment point i -> point i+1
    for features in layer.getFeatures():
        line = features.geometry().asPolyline()
        # add the two end points of a segment as two nodes and an edge (point1 -> point2) to the Graph
        for seg_start, seg_end in pair(line):
            G.add_edges_from([(seg_start, seg_end)])
```

Now we have the nodes and the edges of the Graph:

```Python
     print G.nodes() # = nodes of the polyline in (x,y) form
     [(198133,93258.8), (198757,93232.4), (197592,94296.2), (197579,93232.4), (198761,94274.3), (198146,94287.5)]
     print G.edges() # = all the segments of the polyline
     [((198133,93258.8), (198146,94287.5)), ((198757,93232.4), (198761,94274.3)), ((197592,94296.2), (197579,93232.4)), ((197592,94296.2), (198146,94287.5)), ((198761,94274.3), (198146,94287.5))]
```

We search the nodes which the degree is > 1

```Python
     for i in G.nodes():
        if G.degree(i) > 1:
            print QgsPoint(i), G.degree(i)
     (197592,94296.2) 2
     (198761,94274.3) 2
     (198146,94287.5) 3
```

And we have the desired points:
    
Result:

![enter image description here][9]


  [1]: https://wiki.python.org/moin/PythonGraphApi
  [2]: http://www.python-course.eu/graphs_python.php
  [3]: http://www.python.org/doc/essays/graphs.html
  [4]: http://en.wikipedia.org/wiki/Degree_%28graph_theory%29
  [5]: http://i.stack.imgur.com/gJnoD.jpg
  [6]: http://i.stack.imgur.com/X6oJX.jpg
  [7]: http://networkx.github.io/
  [8]: http://networkx.github.io/documentation/latest/reference/readwrite.nx_shp.html?highlight=shapefile
  [9]: http://i.stack.imgur.com/QdCDs.jpg
  [10]: http://i.stack.imgur.com/srxlD.jpg
