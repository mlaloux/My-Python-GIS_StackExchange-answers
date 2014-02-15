From [OGR Layer Intersection](http://gis.stackexchange.com/questions/82935/ogr-layer-intersection)

There are some errors in your script but it is not the most important problem:

You cannot create a valid shapefile without specifying the geometry of the layer:

```Python
    driver = ogr.GetDriverByName('ESRI Shapefile')
    dstshp = driver.CreateDataSource('SomeFilename.shp')
    dstlayer = dstshp.CreateLayer('mylayer',geom_type=ogr.wkbPolygon)
```

And you don't know a priory the geometry of the resulting intersection layer. The intersection of two polygon layers is different from the intersection of a polygon layer and a polyline layer for example.

For that, you can get the geometry of the intersection by:

For example (with two polygons shapefiles):

```Python
    layer1.GetGeomType()
    3 # -> polygon
    # create an empty geometry of the same type
    union1=ogr.Geometry(3)
    # union all the geometrical features of layer 1
    for feat in layer1:
        geom =feat.GetGeometryRef()
        union1 = union1.Union(geom)
    # same for layer2
    union2=ogr.Geometry(layer2.GetGeomType())
    for feat in layer2:
        geom =feat.GetGeometryRef()  
        union2 = union2.Union(geom)
    # intersection
    intersection = union1.Intersection(union2)
    print intersection.GetGeometryName()
    'MultiPolygon'
```

At this stage, you can save the resulting geometry to a shapefile (without the fields of the original layers):

```Python

    dstshp = driver.CreateDataSource('SomeotherFilename.shp')
    dstlayer = dstshp.CreateLayer('mylayer',geom_type=ogr.wkbMultiPolygon)
```


But if you want to use your script (a MultiPolygon is a collection of Polygons):

```Python
    driver = ogr.GetDriverByName('ESRI Shapefile')
    dstshp = driver.CreateDataSource('SomeFilename.shp')
    dstlayer = dstshp.CreateLayer('mylayer',geom_type=ogr.wkbPolygon)
    for feature1 in layer1:
       geom1 = feature1.GetGeometryRef()
       attribute1 = feature1.GetField('FieldName1')
        for feature2 in layer2:
           geom2 = feature2.GetGeometryRef()
           attribute2 = feature2.GetField('FieldName2') 
           # select only the intersections
           if geom2.Intersects(geom1): 
              intersection = geom2.Intersection(geom1)
              dstfeature = ogr.Feature(dstlayer.GetLayerDefn())
              dstfeature.SetGeometry(intersection)
              dstfeature.setField(attribute1)
              dstfeature.setField(attribute2)
              dstfeature.Destroy() 
```

Don't forget to define the fields before (look at [Python GDAL/OGR Cookbook:Vector Layers][1]). And it is much easier with the module [Fiona][2]

    


  [1]: http://pcjericks.github.io/py-gdalogr-cookbook/vector_layers.html#create-a-new-shapefile-and-add-data
  [2]: http://toblerity.org/fiona/manual.html
