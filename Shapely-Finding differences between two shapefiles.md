from [Shapely-Finding differences between two shapefiles](http://gis.stackexchange.com/questions/80090/finding-differences-between-two-shapefiles-in-python/80524#80524)


Suppose we have two polygons (green and blue):

![enter image description here][1]

They are not equal (as [Fetzer][2] suggest):

```python
    green.equals(blue)
    False
```
and

```python
    blue.equals(green)
    False
```

And we can can determine the [difference][3] (in red):

```python
    blue.difference(green)
```

![enter image description here][4]

and

```python
    green.difference(blue)
```
gives an empty geometry

Thus, you can use a supplementary condition:

    if not poly1.difference(poly2).is_empty:
         process

And if you want to find the nodes that have been modified:

```python
     S1 = set(list(blue.difference(green).exterior.coords)
     S2 = set(list(blue.exterior.coords)
     S3 = set(list(green.exterior.coords)
```

S1 - S2 and S1 - S3 gives the points (two here blue and red):

![enter image description here][5]

and the distance:

```python
    point1.distance(point2)
```

##new : compare multiple polygons:

Here is one solution:  
For that, I use [Fiona][6] to open the polygon shapefiles and save a resulting shapefile with differences:

![enter image description here][7]

```python

    import fiona
    from shapely.geometry import shape
    green = fiona.open("poly1.shp")
    blue = fiona.open("poly2.shp") 
    # test the function difference between green and blue shapefiles
    [not shape(i['geometry']).difference(shape(j['geometry'])).is_empty for i,j in zip(list(green),list(blue))]
    [False, False, False, True]
    # control
    for geom in [shape(i['geometry']).difference(shape(j['geometry'])) for i,j in zip(list(green),list(blue))]:
         print geom
    GEOMETRYCOLLECTION EMPTY
    GEOMETRYCOLLECTION EMPTY
    GEOMETRYCOLLECTION EMPTY
    POLYGON ((-0.0806077747083538 0.6329375155131045, 0.0085568963219002 0.5081069760707490, -0.0816567708381215 0.6025166277498414, -0.1529885076623247 0.5437728444828506, -0.1292856235630944 0.6206937720158269, -0.0806077747083538 0.6329375155131045))
    # test the function difference between blue and green shapefiles
    [not shape(i['geometry']).difference(shape(j['geometry'])).is_empty for i,j in zip(list(blue),list(green))]
    [True, False, False, False]
    # control
    for geom in [shape(i['geometry']).difference(shape(j['geometry'])) for i,j in zip(list(blue),list(green))]:
    POLYGON ((0.2292711598746081 0.7386363636363635, 0.6691026645768023 0.6691026645768023, 0.2440830721003134 0.7205329153605015, 0.1074843260188087 0.3452978056426331, 0.2292711598746081 0.7386363636363635))
    GEOMETRYCOLLECTION EMPTY
    GEOMETRYCOLLECTION EMPTY
    GEOMETRYCOLLECTION EMPTY
    # write a resulting shapefile
    from shapely.geometry import mapping
    schema = {'geometry': 'Polygon','properties': {'test': 'int'}}
    with fiona.open('diff.shp','w','ESRI Shapefile', schema) as e:
         for geom  in [shape(i['geometry']).difference(shape(j['geometry'])) for i,j in zip(list(green),list(blue)]:
             if not geom.is_empty:
                   e.write({'geometry':mapping(geom), 'properties':{'test':1}})
         for geom  in [shape(i['geometry']).difference(shape(j['geometry'])) for i,j in zip(list(blue),list(green))]:
             if not geom.is_empty:
                   e.write({'geometry':mapping(geom), 'properties':{'test':2}})

```

Result: aaaa

![enter image description here][8]


  [1]: http://i.stack.imgur.com/6t3NL.jpg
  [2]: http://gis.stackexchange.com/users/7424/fezter
  [3]: http://toblerity.org/shapely/manual.html#object.difference
  [4]: http://i.stack.imgur.com/uyq97.jpg
  [5]: http://i.stack.imgur.com/0H4m7.jpg
  [6]: http://toblerity.github.com/fiona/manual.html
  [7]: http://i.stack.imgur.com/KMPiR.jpg
  [8]: http://i.stack.imgur.com/P7Sqm.jpg
