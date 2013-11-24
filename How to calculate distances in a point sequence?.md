From [How to calculate distances in a point sequence?](http://gis.stackexchange.com/questions/44064/how-to-calculate-distances-in-a-point-sequence)

I return to this issue because it is very similar to [How do I find vector line bearing in QGIS or GRASS?][1] and it can be solved with Python in the same way:

1) Haversine distance

One can find lots of scripts by searching Haversine distance with Python on the Internet and I choose one of them in [Haversine Formula in Python (Bearing and Distance between two GPS points)][2]

```python
    
    def haversine(lon1, lat1, lon2, lat2):
        """
        Calculate the great circle distance between two points 
        on the earth (specified in decimal degrees)
        """
        # convert decimal degrees to radians 
        lon1, lat1, lon2, lat2 = map(math.radians, [lon1, lat1, lon2, lat2])
        # haversine formula 
        dlon = lon2 - lon1 
        dlat = lat2 - lat1 
        a = math.sin(dlat/2)**2 + math.cos(lat1) * math.cos(lat2) * math.sin(dlon/2)**2
        c = 2 * math.asin(math.sqrt(a)) 
        km = 6367 * c
        return km
```

We have a series of lines (points) in the file that must be treated in pairs (point1 - point2) to calculate the distance. For this we will use a simple iterator from [Most pythonic way to get the previous element][3]

```python

    def offset(iterable):
        prev = None
        for elem in iterable:
            yield prev, elem
            prev = elem
```

Now it is possible to read the file (example of Kerrie) in pairs of lines/points

```python

    import csv
    with open('testhavers.csv', 'rb') as f:
       reader = csv.DictReader(f)
       for  pair in offset(reader):
           print pair
     
     (None, {'LAT': '10.08527', 'LON': '124.59833', 'ID': '1', 'TIME': '21:24:37'})
     ({'LAT': '10.08527', 'LON': '124.59833', 'ID': '1', 'TIME': '21:24:37'},
     {'LAT':    '10.08523', 'LON': '124.59830', 'ID': '2', 'TIME': '21:25:07'})
     ({'LAT': '10.08523', 'LON': '124.59830', 'ID': '2', 'TIME': '21:25:07'}, 
     {'LAT': '10.08526', 'LON': '124.59832', 'ID': '3', 'TIME': '21:25:37'})
     ({'LAT': '10.08526', 'LON': '124.59832', 'ID': '3', 'TIME': '21:25:37'}, 
     {'LAT':    '10.08526', 'LON': '124.59831', 'ID': '4', 'TIME': '21:26:07'})
     
```

Then create a shapefile containing the original fields of the csv file and a new field for the distance with the Python modules Shapely and Fiona of Sean Gillies:

```python

    import fiona
    from shapely.geometry import Point, mapping
    # creation of the schema of the shapefile (geometry and fields)
    schema = { 'geometry': 'Point', 'properties':{'ID': 'int', 'LAT':'float', 'LON':'float', 'TIME':'str','distance' : 'float'}}
    # creation of the shapefile:
    with fiona.collection("result.shp", "w", "ESRI Shapefile", schema) as output:
        # reading the csv file
        with open('testhavers.csv', 'rb') as f:
           reader = csv.DictReader(f)
           # we need here to eliminate the first pair of point with None
           for i, pair in enumerate(offset(reader)):
                if i == 0: (pair with None)
                    # writing of the point geometry and the attributes
                    point = Point(float(pair[1]['LON']), float(pair[1]['LAT']))
                    dist = 0 # None
                    output.write({'properties': {'ID':int(pair[1]['ID']),'LAT':float(pair[1]['LAT']),'LON':float(pair[1]['LON']), 'TIME':pair[1]['TIME'],'distance': dist},'geometry': mapping(point)})
                 else:
                    # writing of the point geometry and the attributes
                    point = Point(float(pair[1]['LON']), float(pair[1]['LAT']))
                    # Haversine distance between pairs of points
                    dist = haversine(float(pair[0]['LON']), float(pair[0]['LAT']), float(pair[1]['LON']),float(pair[1]['LAT']))
                    output.write({'properties': {'ID':int(pair[1]['ID']),'LAT':float(pair[1]['LAT']),'LON':float(pair[1]['LON']), 'TIME':pair[1]['TIME'],'distance': dist},'geometry': mapping(point)})

```
and the result:
![enter image description here][4]

It is also possible to do it with PyQGIS but it is more complex than Fiona which uses simple dictionaries to create shapefiles. 

You can use another function to calculate the Haversine distance ([Why is law of cosines more preferable than haversine when calculating distance between two latitude-longitude points?][5]) without any problem, only the distance calculation changes, not the process of creating the shapefile. 

                     
            


  [1]: http://gis.stackexchange.com/questions/55449/how-do-i-find-vector-line-bearing-in-qgis-or-grass/55480#55480
  [2]: http://stackoverflow.com/questions/4913349/haversine-formula-in-python-bearing-and-distance-between-two-gps-points
  [3]: http://stackoverflow.com/questions/12076270/most-pythonic-way-to-get-the-previous-element/12076386#12076386
  [4]: http://i.stack.imgur.com/7UzpA.jpg
  [5]: http://gis.stackexchange.com/questions/4906/why-is-law-of-cosines-more-preferable-than-haversine-when-calculating-distance-b
