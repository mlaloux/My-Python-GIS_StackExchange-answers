From [Using the python shape library: pyshp - how to convert polygons in .csv file to .shp](http://gis.stackexchange.com/questions/70786/using-the-python-shape-library-pyshp-how-to-convert-polygons-in-csv-file-to)


Your approach is good, but you could make things clearer using dictionaries instead of list and the csv module allows it.   
Moreover, your script use two loops while it is possible to simplify using only one (the second loop is redundant, one line of the csv file = one record of the shapefile).

**1) With dictionaries:**

Reading the csv file:
--------------------

```python
    with open('your.csv', 'rb') as f:
        reader = csv.DictReader(f)
        for row in reader:
            print row
     {'xr': '22.5', 'yb': '31.353634', 'name': 'some Name', 'xl': '-25.3125', 'yt': '47.517193'}
     {'xr': '-0.703125', 'yb': '74.40216', 'name': 'another Name', 'xl': '-103.359375', 'yt': '80.87282'} 
```

You can now use row['xr'] or row['name']  instead of row[n] (more explicit) 

So your script becomes:

```python

    import csv
    polyName, polyPart = [],[]
    with open('your.csv', 'rb') as f:
       reader = csv.DictReader(f)
       for row in reader:
           bl  = [float(row['xl']),float(row['yb'])]
           tl  = [float(row['xl']),float(row['yt'])]
           br  = [float(row['xr']),float(row['yb'])]
           tr  = [float(row['xr']),float(row['yt'])]
           parr = [tl, tr, br, bl, tl]
           polyName.append(row['name'])
           polyPart.append(parr)
```

Writing the polygon shapefile
--------------------  

If you look at [PyShpDocs][1] you can see that:

A polygon is defined by:

```python

    w = shapefile.Writer(shapefile.POLYGON)
    w.line(parts=[[[1,5],[5,5],[5,1],[3,3],[1,1]]])
```

and the script, as you  wrote is:

```python

     import shapefile
     # create the Polygon shapefile
     w = shapefile.Writer(shapefile.POLYGON)
     # the field
     w.field('name','C',maxStringLength)
     # write the polygons in the shapefile
     for part,name in zip(polyPart, polyName):
          w.poly(parts=[part])
          w.record(name) 
     #save the shapefile
     w.save('your.shp')
```

**2) Final solution using only one loop** 

But the second loop is not necessary here: you can do it all with one loop (reading the csv file and writing the shapefile without using the polyName and polyPart lists).

```python
    w = shapefile.Writer(shapefile.POLYGON)
    w.field('name','C',50)
    with open('your.csv', 'rb') as f:
        reader = csv.DictReader(f)
        for row in reader:
            bl  = [float(row['xl']),float(row['yb'])]
            tl  = [float(row['xl']),float(row['yt'])]
            br  = [float(row['xr']),float(row['yb'])]
            tr  = [float(row['xr']),float(row['yt'])]
            parr = [tl, tr, br, bl, tl]
            w.poly(parts=[parr])
            w.record(row['name'])

    w.save("your.shp')
```


Result in QGIS:  

![enter image description here][2]


  [1]: http://code.google.com/p/pyshp/wiki/PyShpDocs
  [2]: http://i.stack.imgur.com/hNMLC.jpg
