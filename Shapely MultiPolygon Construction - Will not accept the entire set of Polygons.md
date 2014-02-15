From [Shapely MultiPolygon Construction - Will not accept the entire set of Polygons](http://gis.stackexchange.com/questions/83084/shapely-multipolygon-construction-will-not-accept-the-entire-set-of-polygons)
look at [Creating Shapely MultiPolygons from shape file MultiPolygons][1] and I detail below the process:


1) browse through the shapefile :

 - with next()

```Python

    import fiona
    c = fiona.open('polygons.shp')
    # the result is a Python iterator, so to read the first feature of the shapefile
    print c.next()
    # the result is a Python dictionary (GeoJSON)format
    {'geometry': {'type': 'Polygon', 'coordinates': [[(249744.23153029341, 142798.16434689672), (250113.79108725351, 142132.95714436853), (250062.62130244367, 141973.76225829343), (249607.77877080048, 141757.71205576291), (249367.77424759799, 142304.68402918623), (249367.77424759799, 142304.68402918623), (249744.23153029341, 142798.16434689672)]]}, 'type': 'Feature', 'id': '0', 'properties': OrderedDict([(u'id', 1), (u'couleur', 1), (u'id_class', None)])}
    # if you want the geometry only
    print c.next()['geometry']
    {'type': 'Polygon', 'coordinates': [[(249175.78991730965, 142292.53526406409), (249367.77424759799, 142304.68402918623), (249607.77877080048, 141757.71205576291), (249014.45396077307, 141876.13484290778), (249175.78991730965, 142292.53526406409)]]}
    ....
```

but as all the iterators, this method raises a built-in StopIteration exception at the end of the iterator or end of the file: 

```Python

    c.next()
    Traceback (most recent call last): .....   
    StopIteration
```

 - so the solution is a list (iterator without the StopIteration exception)

```Python

    c = fiona.open('polygons.shp')
    features = list(c)
    # and the result is a list of all the features in the shapefile:
    # or
    for features in fiona.open('polygons.shp'):
         ....
```

2) Therefore to convert the polygons to a multipolygon:

 
```Python

    from shapely.geometry import MultiPolygo
    multi = []
    # append the geometries to the list
    for pol in fiona.open('polygons.shp'):
        multi.append(shape(pol['geometry'])
    # create the MultiPolygon from the list of Polygons
    Multi = MultiPolygon(multi)
    print Multi.wkt
    'MULTIPOLYGON (((249744.2315302934148349 142798.1643468967231456, 250113.7910872535139788 142132.9571443685272243, ..., 249870.8182051893090829 142570.3083320840960369)))'

```
Or in one line with a list comprehension:

```Python

    Multi = MultiPolygon([shape(pol['geometry']) for pol in fiona.open('polygons.shp')]
```
    
Note:

 If there are different types of geometries in the shapefile (MultiPolygon embedded in polygons)  use [unary_union][2]: "the unary union function can operate on different geometry types, not only polygons as is the case for the older cascaded unions"

```Python

    from shapely.ops import unary_union
    polygon1 = Polygon([(0, 0), (1, 1), (1, 0)])
    polygon2 = Polygon([(1, 1), (2,2), (2, 0)])
    poly  = MultiPolygon([polygon1,polygon2])
    list = [polygon1,polygon2,poly]
    result = unary_union(list)
    print result
    MULTIPOLYGON (((0.00 0.00, 1.00 1.00, 1.00 0.00, 0.00 0.00)), ((1.00 1.00, 2.00 2.00, 2.00 0.00, 1.0 1.00)))
```
So:

```Python

    geoms =[shape(feature['geometry']) for feature in fiona.open("polygons.shp")]
    result = unary_union(geoms) 
```


  [1]: http://gis.stackexchange.com/questions/70591/creating-shapely-multipolygons-from-shape-file-multipolygons/70608#70608
  [2]: http://toblerity.org/shapely/manual.html#shapely.ops.unary_union
