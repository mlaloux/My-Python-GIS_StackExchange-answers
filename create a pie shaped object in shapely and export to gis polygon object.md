from [create a pie shaped object in shapely and export to gis polygon object](http://gis.stackexchange.com/questions/84412/create-a-pie-shaped-object-in-shapely-and-export-to-gis-polygon-object)

You should use [Fiona][1] and the `mapping` function of shapely


```Python
    from shapely.geometry import mapping
    import fiona
    # schema of the shapefile (or GeoJSON file, or...) for the lines
    schema = {'geometry': 'LineString','properties': {'test': 'int'}}
    with fiona.open('lines.shp','w','ESRI Shapefile', schema) as e:
       for i in lines:
           e.write({'geometry':mapping(i), 'properties':{'test':1}})
```

Result:

![enter image description here][2]

You can do the same thing with the buffers but you'll never get a ring polygon as result with your solution (only a superposition of polygons)

You need to use the solution given by [MappaGnosis][3] in [Does shapely within function identify inner holes?][4]

```Python

    one = list(buffers[1].exterior.coords)
    interior = LinearRing(one)
    exterior = LinearRing(list(buffers[2].exterior.coords)
    ring = Polygon(exterior,[interior])
    #  new schema
    schema = {'geometry': 'Polygon','properties': {'test': 'int'}}
    # write the shapefile
    with fiona.open('ring.shp','w','ESRI Shapefile', schema) as e:
        e.write({'geometry':mapping(ring), 'properties':{'test':1}})
```

 Result:
![enter image description here][5]


  [1]: http://toblerity.org/fiona/manual.html
  [2]: http://i.stack.imgur.com/0HABx.jpg
  [3]: http://gis.stackexchange.com/users/5222/mappagnosis
  [4]: http://gis.stackexchange.com/questions/72306/does-shapely-within-function-identify-inner-holes
  [5]: http://i.stack.imgur.com/nycPm.jpg
