From [Convert points to lines with Python GDAL]


Why not work globally ?

 1. calculate the distances between all points
 2. union the resulting lines pointx - pointy with a distance < 14m

I will use [Shapely][1], much easier for resolving these kinds of problems.
You must iterate through all pairs of points to calculate the distance once (as distance point1-point2 = distance point2-point1). There are many solutions in Python and I choose the itertools standard module with [combinations][2].

example:

```python

    myPointDict = {0:(1,1), 1:(2,2), 2:(3,3),3:(4,4),4:(5,5)}
    import itertools
    for i in  itertools.combinations(PointDict.values(), 2):
       print i
    ((1, 1), (2, 2))
    ((1, 1), (3, 3))
    ((1, 1), (4, 4))
    ((1, 1), (5, 5))
    ((2, 2), (3, 3))
    ((2, 2), (4, 4))
    ((2, 2), (5, 5))
    ((3, 3), (4, 4))
    ((3, 3), (5, 5))
    ((4, 4), (5, 5))
```

With ogr (look at [the Python GDAL/OGR Cookbook!][3]):

```python

    point = ogr.Geometry(ogr.wkbPoint)
    point.AddPoint(x,y)
    distance =  point1.distance(point2)
    line = ogr.Geometry(ogr.wkbLineString) 
        line.AddPoint(x1, y1)
        ....
        line.AddPoint(xn,yn)
```

With shapely:

```python

    point = Point(x,y)
    distance = Point(x1,y1).distance(Point(x2,y2)
    linestring = LineString([point1,..., pointn] 
```

So, in your case:

```python

    from shapely.geometry import Point, LineString
    # creation of a empty line for unioning the resulting geometries
    line = LineString()
    for i in  itertools.combinations(pointDict.values(), 2):
         # if distance < 14m union the line ptx-pty to line
         if Point(i[0]).distance(Point(i[1])) < 14:
                line = line.union(LineString([(Point(i[0]).x, Point(i[0]).y), (Point(i[1]).x, Point(i[1]).y)]))
         # result 
         print line.wkt
         'MULTILINESTRING ((345672.493225679441821 1267286.555012494325638,345681.57590266619809 1267286.555012494325638),(345672.493225679441821 1267286.555012494325638,345663.410548692685552 1267277.472335507394746),(345672.493225679441821 1267286.555012494325638,345681.57590266619809 1267277.472335507394746),(345681.57590266619809 1267286.555012494325638,345681.57590266619809 1267277.472335507394746),(345654.327871705929283 1267277.472335507394746,345663.410548692685552 1267277.472335507394746),(345654.327871705929283 1267277.472335507394746,345645.245194719173014 1267268.389658520696685),(345681.57590266619809 1267277.472335507394746,345690.658579652954359 1267268.389658520696685),(345636.162517732358538 1267268.389658520696685,345645.245194719173014 1267268.389658520696685),(345636.162517732358538 1267268.389658520696685,345627.079840745602269 1267259.306981533998623),(345690.658579652954359 1267268.389658520696685,345681.57590266619809 1267259.306981533998623),(345608.914486772089731 1267259.306981533998623,345617.997163758846 1267259.306981533998623),(345608.914486772089731 1267259.306981533998623,345599.831809785333462 1267250.224304547300562),(345617.997163758846 1267259.306981533998623,345627.079840745602269 1267259.306981533998623),(345681.57590266619809 1267259.306981533998623,345672.493225679441821 1267250.224304547300562),(345590.749132798577193 1267250.224304547300562,345599.831809785333462 1267250.224304547300562),(345672.493225679441821 1267250.224304547300562,345663.410548692685552 1267241.14162756036967))'
```

And if you want to use the end of your script:
```python
    multiline = ogr.CreateGeometryFromWkt(line.wkt)
```
or using [Fiona][4] (an easier Python wrapper of the ogr library)

```python
    import fiona
    from shapely.geometry import mapping
    # schema of the shapefile
    schema = {'geometry': 'MultiLineString','properties': {'test': 'int'}
    with fiona.open('myshp3.shp','w','ESRI Shapefile', schema) as c:
           record =  = {'geometry':mapping(line), 'properties':{'test':1}}
           c.write(record)
```
Result:

![enter image description here][5]

But, I do not know if this is what you want.


  [1]: http://gispython.org/shapely/manual.html
  [2]: http://docs.python.org/2.7/library/itertools.html#itertools.combinations
  [3]: http://pcjericks.github.io/py-gdalogr-cookbook/geometry.htm
  [4]: http://toblerity.org/fiona/manual.html
  [5]: http://i.stack.imgur.com/HE9kx.jpg
