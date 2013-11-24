From [Fiona - Preffered method for defining a schema](http://gis.stackexchange.com/questions/74858/fiona-preffered-method-for-defining-a-schema)


I don't really understand your problem. Some explanations: for example, we want to

 1. create a new shapefile
 2. modify the original schema: for that, it's easier to just copy things to a new shapefile and make the changes as you copy

 - Create a new Polyline shapefile:

```python

    import fiona
    # schema: it is a simple dictionary with geometry and properties as keys
    schema = {'geometry': 'LineString','properties': {'test': 'int'}}
    # for defining the geometry, you need Shapely
    from shapely.geometry import LineString, mapping
    # two simples geometries
    lines = [LineString([(272830.63,155125.73),(273770.32,155467.75)]),LineString([(273536.47,155914.07),(272033.12,152265.71)])]
    with fiona.open('myshp.shp', 'w', 'ESRI Shapefile', schema) as layer:
        for line in lines:
            # filling schema
            elem = {}
            # geometry with mapping function of shapely
            elem['geometry'] = mapping(line) 
            # attribute value (the same here)
            elem['properties'] = {'test': 145}
            # writing element in the file
            layer.write(elem)
```

 - Now we want to modify the schema of the original shapefile in a new shapefile:

1) Open the original shapefile:

```python

    shapefile =fiona.open('myshp.shp')
    #read the schema
    schema2 = shapefile.schema
    print schema2
    {'geometry': 'LineString', 'properties': OrderedDict([(u'test', 'int:10')])}
```

2) As it is a dictionary, it is easy to add new fields/keys in the properties:

```python

    schema2['properties']['string']='str'
```

3) Now we create a new shapefile copying myshp.shp with the new schema :

```python

    with fiona.open('myshp.shp', 'r') as input:
        schema = schema2
        # writing the new shapefile
        with fiona.open('myshp_copy.shp', 'w', 'ESRI Shapefile', schema) as output:
            for elem in input:
                # add the new attribute value
                elem['properties']['string']="hello"
                output.write({'properties': elem['properties'],'geometry': mapping(shape(elem['geometry']))})
```

 - verification

```python

    c = fiona.open('myshp_copy.shp')
    c.schema
    {'geometry': 'LineString', 'properties': OrderedDict([(u'test', 'int:10'), (u'string', 'str')])}
    # first element of the shapefile
    c.next()
    {'geometry': {'type': 'LineString', 'coordinates': [(272830.63, 155125.73000000001), (273770.32000000001, 155467.75)]}, 'type': 'Feature', 'id': '0', 'properties': OrderedDict([(u'test', 145), (u'string', u'hello')])}
```

 - Conclusion

If you don't want to modify the shapefile, it is easier with **schema.copy()** that is only used to get a copy of the original schema (no definition here)

```python
    with fiona.open('myshp.shp', 'r') as input:
        schema = input.schema.copy()
         with fiona.open('myshp_copy2.shp', 'w', 'ESRI Shapefile', schema) as output:
             for elem in input:
                 output.write({'properties': elem['properties'],'geometry': mapping(shape(elem['geometry']))})
```
