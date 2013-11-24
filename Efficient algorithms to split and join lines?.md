From [Efficient algorithms to split and join lines?](http://gis.stackexchange.com/questions/61474/efficient-algorithms-to-split-and-join-lines)


You can use effectively Shapely, and [Fiona][1] to read a shapefile for example:

```python
    import fiona
    # open a line shapefile
    file = fiona.open('lines.shp')
    # first element of the shapefile
    first = file.next
    print first
    {'geometry': {'type': 'LineString', 'coordinates': [(203317.23, 90448.75), (203679.62, 90105.68), (203882.57, 89902.74), (204143.49, 89641.81), (204394.75, 89385.72), (204563.87, 89235.93)]}, 'id': '0', 'properties': {u'id': "1"}}
```

Now import Shapely

```python
    from shapely.geometry import Point, LineString, shape
    geom = shape(first['geometry'])
    # now it is a Shapely geometry
    print geom
    LINESTRING (203317.23 90448.75, 203679.62 90105.68, 203882.57 89902.74, 204143.49 89641.81, 204394.75 89385.72, 204563.87 89235.93)
```  

**Splitting**

**We can split the line in segments**, each pair of coordinates (Point) define a line segment:

```python
    def pair(list):
        '''Iterate over pairs in a list -> pair of points '''
        for i in range(1, len(list)):
            yield list[i-1], list[i]

    for seg_start, seg_end in pair(geom.coords):
        line_start = Point(seg_start)
        line_end = Point(seg_end)
        segment = LineString([line_start.coords[0],line_end.coords[0]])
        print segment
    LINESTRING (203317.23 90448.75, 203679.62 90105.68)
    LINESTRING (203679.62 90105.68, 203882.57 89902.74)
    LINESTRING (203882.57 89902.74, 204143.49 89641.81)
    LINESTRING (204143.49 89641.81, 204394.75 89385.72)
    LINESTRING (204394.75 89385.72, 204563.87 89235.93)
```

You could do it directly with Fiona:
 
```python
    for seg_start, seg_end in paires(first['geometry']['coordinates']):
        ....
```

**Union**

 1. If the line is straight, simply use the first and the last point to make the LineString   
 
```python
    LINESTRING (203317.23 90448.75, 204563.87 89235.93)
```
 2. In other cases, with shapely, you can use union, cascaded_union(geoms) or unary_union(geoms) and the result is a MULTILINESTRING;

With union:

```python
    line = LineString()
    for element in line['geometry']['coordinates']:
       geom =shape(line['geometry'])
       profile = profile.union(geom)
    print line
    MULTILINESTRING ((203317.23 90448.75, 203679.62 90105.68), (203679.62 90105.68, 203882.57 89902.74), (203882.57 89902.74, 204143.49 89641.81), (204143.49 89641.81, 204394.756 89385.72), (204394.75 89385.72, 204563.87 89235.93))
```
or directly with shapely:

```python
    line = LineString()
    for seg_start, seg_end in paires(geom.coords):
         line = line.union(LineString([Point(seg_start).coords[0],Point(seg_end).coords[0]]))
```

**Intersection**

The shapely function is geom1.intersection(geom2)

 1. for two straight lines, it is simply
 ```python

    line1.intersection(line2) # the result is a Point
```
 2. for two lines with segments, it is

```python
    (union of segments of line1).intersection(union of segments of line2) # the result is a Point or a MultiPoint (more than a single intersection)
```
And saving the results is easy with Fiona 


  [1]: https://pypi.python.org/pypi/Fiona
