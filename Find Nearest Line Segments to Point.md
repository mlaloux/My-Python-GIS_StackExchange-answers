From [Find Nearest Line Segments to Point](http://gis.stackexchange.com/questions/81613/find-nearest-line-segments-to-point/)

I have reproduced your example with shapefiles. 

You can use [Shapely][1] and [Fiona][2] to solve your problem.

####1) Your problem (with a shapely `Point`):

![enter image description here][3]

####2) starting with an arbitrary line (with an adequate length):


```python

    from shapely.geometry import Point, LineString
    line = LineString([(point.x,point.y),(final_pt.x,final_pt.y)])
```


![enter image description here][4]

####3) using [shapely.affinity.rotate][5] to create the radii (rotating the line from the point, look also the [Mike Toews][6]'s answer at [Python, shapely library: is it possible to do an affine operation on shape polygon?][7]):


```python

    from shapely import affinity
    # Rotate i degrees CCW from origin at point (step 10°)
    radii= [affinity.rotate(line, i, (point.x,point.y)) for i in range(0,360,10)]
    

```

![enter image description here][8]

####4) now, using [shapely:cascaded_union][9] (or [shapely:unary_union][10]) to get a MultiLineString:

<!-- language: lang-py -->

    from shapely.ops import cascaded_union
    mergedradii = cascaded_union(radii)
    print mergedradii.type
    MultiLineString
    

####5) the same with the original lines (shapefile)


```python

    import fiona
    from shapely.geometry import shape
    orlines = fiona.open("orlines.shp")
    shapes = [shape(f['geometry']) for f in orlines]
    mergedlines = cascaded_union(shapes)
    print mergedlines.type
    MultiLineString

```

####6) the intersection between the two multigeometries is computed and the result is saved to a shapefile:


```python

     points =  mergedlines.intersection(mergedradii)
     print points.type
     MultiPoint
     from shapely.geometry import mapping
     schema = {'geometry': 'MultiPoint','properties': {'test': 'int'}}
     with fiona.open('intersect.shp','w','ESRI Shapefile', schema) as e:
          e.write({'geometry':mapping(points), 'properties':{'test':1}})

```

Result:

![enter image description here][11]

####7) but, problem, if you use a a longer radius, the result is different:

![enter image description here][12]

####8) And if you want to get your result, you need to select only the point with the shortest distance from the original point on a radius: 


```python


    points_ok = []
    for line in mergeradii:
       if line.intersects(mergedlines):
           if line.intersection(mergedlines).type == "MultiPoint":
              # multiple points: select the point with the minimum distance
              a = {}
              for pt in line.intersection(merged):
                  a[point.distance(pt)] = pt
              points_ok.append(a[min(a.keys())])
           if line.intersection(mergedlines).type == "Point":
              # ok, only one intersection
              points_ok.append(line.intersection(mergedlines))
    solution = cascaded_union(points_ok)
    schema = {'geometry': 'MultiPoint','properties': {'test': 'int'}}
    with fiona.open('intersect3.shp','w','ESRI Shapefile', schema) as e:
         e.write({'geometry':mapping(solution), 'properties':{'test':1}})


```

Final result:

![enter image description here][13]

I hope that is what you want.

 


  [1]: http://toblerity.org/shapely/manual.html
  [2]: http://toblerity.org/fiona/manual.htm
  [3]: http://i.stack.imgur.com/CrbO3.jpg
  [4]: http://i.stack.imgur.com/vhnvh.jpg
  [5]: https://github.com/Toblerity/Shapely/blob/master/shapely/affinity.py
  [6]: http://gis.stackexchange.com/users/1872/mike-toews
  [7]: http://gis.stackexchange.com/questions/13383/python-shapely-library-is-it-possible-to-do-an-affine-operation-on-shape-polyg/13431#13431
  [8]: http://i.stack.imgur.com/5xvF6.jpg
  [9]: http://toblerity.org/shapely/manual.html#shapely.ops.cascaded_union
  [10]: http://toblerity.org/shapely/manual.html#shapely.ops.unary_union
  [11]: http://i.stack.imgur.com/OaYmn.jpg
  [12]: http://i.stack.imgur.com/YF5Ns.jpg
  [13]: http://i.stack.imgur.com/QR1ko.jpg
