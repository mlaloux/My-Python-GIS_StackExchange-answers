From [how would one get the end points of a polyline?](http://gis.stackexchange.com/questions/86040/how-would-one-get-the-end-points-of-a-polyline)

It is easier with [Fiona][1], more "Pythonic",  and list slicing:

```python
    import fiona
    with fiona.drivers():
       for line in fiona.open("some_shapefile.shp"):
             # print first and last point of every line
             print line['geometry']['coordinates'][0], line['geometry']['coordinates'][-1]
```

And with [shapely][2]:

```python

    from shapely.geometry import Point
    for line in fiona.open("some_shapefile.shp"):
       print Point(line['geometry']['coordinates'][0]), Point(line['geometry']['coordinates'][-1])
```

And you can construct you polygon and save it with Fiona


New: using the suggestion of sgillies (boundary) with the shape function of shapely

```python
    
    from shapely.geometry import shape
    for line in fiona.open("some_shapefile.shp"):
         print shape(line['geometry']).boundary[0], shape(line['geometry']).boundary[1]

```


  [1]: http://toblerity.org/fiona/manual.html
  [2]: http://toblerity.org/shapely/manual.html
