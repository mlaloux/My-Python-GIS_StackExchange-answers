
From [How to access vector coordinates in GRASS GIS from python?](http://gis.stackexchange.com/questions/28061/how-to-access-vector-coordinates-in-grass-gis-from-python)

For walking a graph you have all the v.net family.
To extract the vertices coordinates of lines or polygons (and other columns) one solution is, as always, through v.out.ascii that gives you the xy(z) coordinates(see [How one can find the starting and end point of a line in a vector file or how one can connect two line end to end using C code][1])

```python
    test = grass.read_command("v.out.ascii", input="yourvector", format="standard")
``` 
The result is given in [v.out.ascii][2]. In my case, for example, the variable test is:

    ORGANIZATION: 
    DIGIT DATE: 
    MAP NAME:  
    MAP DATE:     Sun Jun 24 10:16:35 2012
    MAP SCALE:    1
    OTHER INFO:  
    ZONE:         0
    MAP THRESH:   0.000000
    VERTI:
    L  7 1
    206643.21517601 125181.18058576
    201007.33432923 121517.8555206
    208615.77587567 118699.91687157
    199034.77765775 115036.59058769
    200725.54321492 111936.8560102
    192835.30987687 107428.14794157
    192835.30987687 107428.14794157
    1 1

It is a (poly)line with one element and 7 vertices (L 7 1)  and test is a Python string so
```python
    result = test.split("\n") 
```
and you have a list with the lines of test. If you use regular expressions:

```python
    for line in result:
    if re.findall(r'^.[0-9]+\.',line):
        print line

     206643.21517601 125181.18058576
     201007.33432923 121517.8555206
     208615.77587567 118699.91687157
     199034.77765775 115036.59058769
     200725.54321492 111936.8560102
     192835.30987687 107428.14794157
     192835.30987687 107428.14794157
```
and 
```python
    coords = []    
    for line in result:
       if re.findall(r'^.[0-9]+\.',line): 
           coords.append(line.strip().split(" "))
```
gives you the solution   
```python

    [['206643.21517601', '125181.18058576'], ['201007.33432923', '121517.8555206'], ['208615.77587567', '118699.91687157'], ['199034.77765775', '115036.59058769'], ['200725.54321492', '111936.8560102'], ['192835.30987687', '107428.14794157'], ['192835.30987687', '107428.14794157'], ['206643.21517601', '125181.18058576'], ['201007.33432923', '121517.8555206'], ['208615.77587567', '118699.91687157'], ['199034.77765775', '115036.59058769'], ['200725.54321492', '111936.8560102'], ['192835.30987687', '107428.14794157'], ['192835.30987687', '107428.14794157']]
```
After that, you can create a new point vector file with the xy values and [How one can find the starting and end point of a line in a vector file or how one can connect two line end to end using C code][1]


If you want to use OGR python bindings, it is easy

```python
    from osgeo import ogr
    # open grass vector
    ds = ogr.Open('/grassdata/geol/test/vector/testline/head')
    layer = ds.GetLayer(0)
    layer.GetName()
    'testline'
     feat = layer.GetFeature(0)
     geom = feature.GetGeometryRef()
     geom.ExportToWkt()
     'LINESTRING (206643.21517600651714 125181.180585758760571,201007.334329231875017 121517.855520597659051,208615.775875667924993 118699.916871574707329,199034.77765774706495 115036.59058768954128,200725.543214922072366 111936.85601020231843,192835.30987687263405 107428.147941565141082,192835.30987687263405 107428.147941565141082)'
     for i in range(geometry.GetPointCount()):
         xy = geometry.GetPoint(i)
         print xy
 
    (206643.21517600652, 125181.18058575876, 0.0)
    (201007.33432923188, 121517.85552059766, 0.0)
    (208615.77587566792, 118699.91687157471, 0.0)
    (199034.77765774706, 115036.59058768954, 0.0)
    (200725.54321492207, 111936.85601020232, 0.0)
    (192835.30987687263, 107428.14794156514, 0.0)
    (192835.30987687263, 107428.14794156514, 0.0)
```

After that you can use other modules like shapely

```python    
    from shapely.wkt import loads
    line =loads(geometry.ExportToWkt())
    list(line.coords) 
    [(206643.21517600652, 125181.18058575876), (201007.33432923188, 121517.85552059766), (208615.77587566792, 118699.91687157471), (199034.77765774706, 115036.59058768954), (200725.54321492207, 111936.85601020232), (192835.30987687263, 107428.14794156514), (192835.30987687263, 107428.14794156514)]

```


  [1]: http://osgeo-org.1560.n6.nabble.com/How-one-can-find-the-starting-and-end-point-of-a-line-in-a-vector-file-or-how-one-can-connect-two-lie-td4542252.html
  [2]: http://grass.fbk.eu/gdp/html_grass64/v.in.ascii.html
