From [How to find the polygons bordering a feature programmatically with qgis?](http://gis.stackexchange.com/questions/86591/how-to-find-the-polygons-bordering-a-feature-programmatically-with-qgis)

In the given solution, Ujaval Gandhi uses [shapely][1]  to find all neighboring polygons of each of the polygons in a layer (function shapely [object.touches(other)][2]).

In PyQGIS 2.x, this function is available (function PyQGIS `object.touches(other)`)

An example:

![enter image description here][3]

For iterating over the features/geometry and compare polygon by polygon I will use the standard [itertools][4] module in the console:

```python
    layer = qgis.utils.iface.activeLayer()
    import itertools
    # with itertools permutations, length of the permutations(2) in this case.
    for geom1,geom2 in itertools.permutations(layer.getFeatures(),r=2):
          if geom1.geometry().touches(geom2.geometry()):
                print geom1.attributes(),geom2.attributes()
    [3] [1]
    [2] [1]
    [1] [3]
    [1] [2]
    # with itertools combinations, length of the combinations(2) in this case.
    for geom1,geom2 in itertools.combinations(layer.getFeatures(),r=2):
          if geom1.geometry().touches(geom2.geometry()):
                print geom1.attributes(),geom2.attributes()
                layer.select(geom1.id())
     [3] [1]
     [2] [1]

```
![enter image description here][11]

no neighbours (function `disjoint`):

```python

     for geom1,geom2 in itertools.combinations(layer.getFeatures(),r=2):
          if geom1.geometry().disjoint(geom2.geometry()):
          print geom1.attributes(),geom2.attributes()
     [4] [3]
     [4] [2]
     [4] [1]
     [3] [2]
```

And the intersections:

```python

    for geom1,geom2 in itertools.combinations(layer.getFeatures(),r=2):
        if geom1.geometry().intersects(geom2.geometry()):
              print geom1.attributes(),geom2.attributes()
    [3] [1]
    [2] [1]
    
```

![enter image description here][18]


  [1]: http://gispython.org/shapely/manual.html
  [2]: http://toblerity.org/shapely/manual.html#object.touches
  [3]: http://i.stack.imgur.com/Pv0S3.jpg
  [4]: http://pythonarticles.com/itertools.html
  [5]: http://gispython.org/shapely/manual.html
  [6]: http://gispython.org/shapely/manual.html
  [7]: http://i.stack.imgur.com/Pv0S3.jpg
  [8]: http://toblerity.org/shapely/manual.html#object.touches
  [9]: http://gispython.org/shapely/manual.html
  [10]: http://gispython.org/shapely/manual.html
  [11]: http://i.stack.imgur.com/rvr9w.jpg
  [12]: http://i.stack.imgur.com/Pv0S3.jpg
  [13]: http://toblerity.org/shapely/manual.html#object.touches
  [14]: http://gispython.org/shapely/manual.html
  [15]: http://toblerity.org/shapely/manual.html#object.touches
  [16]: http://gispython.org/shapely/manual.html
  [17]: http://gispython.org/shapely/manual.html
  [18]: http://i.stack.imgur.com/kGUEk.jpg
