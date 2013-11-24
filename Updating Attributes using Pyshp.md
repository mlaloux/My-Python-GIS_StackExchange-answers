
from [Updating Attributes using Pyshp](is.stackexchange.com/questions/57635/updating-attributes-using-pyshp/57696)


It is the same thing with pyshp, except that you cannot update directly the dbf file.
When you read a shapefile, the data are stored in Python lists 

```python
    import shapefile
    input = shapefile.Reader("yourfile.shp")
    shapes = input.shapes() # -> the geometries in a list
    fields = input.fields[1:] -> the fields definition in a list
    fields_name = = [field[0] for field in fields] -> the fields names in a list
    attributes = input.records() -> the attributes in a list
    
    # now you can modify an attribute value in the list:
    attributes[0][1] = "what you want" # second attribute of the first record
    # if you want to use a field name for the attribute, like in dbfpy, you must use a dictionary
    list = [(i,dict(zip(fields, attri))) for i,attri in enumerate(attributes)]
    dict = dict((key, value) for (key, value) in list)
    # now you can modify an attribute value in the dictionary:
    dict[0]['field']='whatyouwant' # attribute of "field" in the first record
    # and return to the original attributes list
    attributes_cor = [i.values() for i in dict.values()]
```

but this changes the value in the list or in the dictionary, not in the dbf file. To do this, rather than affecting the original shapefile, it is better to create a copy with the new attribute list (same as [Add a Field to an Existing Shapefile ][2] or [Subsetting a Shapefile by Attributes ][3]).


You can also use other Python libraries like ogr or [Fiona][4] (see [Using the API of QSpatiaLite plugin][5] for the principles, the data are stored as Python dictionaries)

```python
    from shapely.geometry import mapping, shape
    import fiona
    # Read the original Shapefile
    with fiona.collection('yourfile.shp', 'r') as input:
    # The output has the same schema
    schema = input.schema.copy()
    # write a new shapefile
    with fiona.collection('yourcopyfile.shp', 'w', 'ESRI Shapefile', schema) as output:
        for elem in input:
             elem['properties']['field'] = 'whatyouwant' # or a function, or...
             output.write({'properties': elem['properties'],'geometry': mapping(shape(elem['geometry']))})
```
    
    


  [1]: http://geospatialpython.com/2013/04/add-field-to-existing-shapefile.html
  [2]: http://geospatialpython.com/2013/04/add-field-to-existing-shapefile.html
  [3]: http://geospatialpython.com/2010/12/subsetting-shapefile-by-attributes.html
  [4]: https://pypi.python.org/pypi/Fiona
  [5]: http://gis.stackexchange.com/questions/57208/using-the-api-of-qspatialite-plugin/57215#57215
