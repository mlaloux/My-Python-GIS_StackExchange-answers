from [Python GDAL/OGR: retrieved feature is NoneType](http://gis.stackexchange.com/questions/65832/python-gdal-ogr-retrieved-feature-is-nonetype/65847)


It is a pure Python problem and not an GDAL/OGR problem. But if you want to program in Python,you need to understand the difference between an iterator and an iterable (many, many references on the Web, but one of the best is [Loop Like A Native][1] by Ned Batchelder). These are fundamental concepts in Python.

 
> "The important operation on an iterable is **iter()**, which will return an iterator. And the only operation available on an iterator is **next()**, which either returns the next value, or raises StopIteration, a special exception that means the iteration is finished." (from Ned Batchelder)

An iterable produces an iterator and an iterator produces a stream of values. 

**So what's the difference between the for loop and  pointsLayer.GetNextFeature() ?**

**the for loops = iterator and pointsLayer= iterable**  

```python

    for feature in iterable:
       statements
```
so with your example:

```python
    from osgeo import ogr
    source = ogr.Open('yourpointshapefile.shp')
    pointsLayer = source.GetLayer()
    for feature in pointsLayer:
        geom =feature.GetGeometryRef()
        xy = geom.GetPoint()
        print xy

    (272022.68669955182, 155404.12013724342, 0.0)
    (272904.99338241993, 152881.6706538822, 0.0)
    .....
    (272796.14718989708, 152075.00336062044, 0.0)
```


  [1]: http://nedbatchelder.com/text/iter.html

But we can create another type of iterator with iter() and next():

```python

    source = ogr.Open('yourpointshapefile.shp')
    pointsLayer = source.GetLayer()
    iterator = iter(pointsLayer)
    # first feature in pointsLayer
    feature = iterator.next()
    geom = feature.GetGeometryRef()
    xy = geom.GetPoint()
    print xy
    (272022.68669955182, 155404.12013724342, 0.0)
    # second feature in pointsLayer
    feature = iterator.next()
    geom = feature.GetGeometryRef()
    xy = geom.GetPoint()
    print xy
    (272904.99338241993, 152881.6706538822, 0.0)
    ....
    # end raises StopIteration error to signal that iteration is complete
    feature = iterator.next()
    Traceback (most recent call last):
    ...
    StopIteration
```

Unlike the for loop that handles the StopIteration error, you need here another control structure (while loop,if,...)

**Thus, what is pointsLayer.GetNextFeature() ?**

It is an iterator and you can replace iterator = iter(pointsLayer) and iterator.next() by 

```python

    feature = pointsLayers.GetNextFeature()
    geom = feature.GetGeometryRef()
    xy = geom.GetPoint()
    .....
    feature = pointsLayers.GetNextFeature()
    ....
```


**I hope that I have been able to explain why you should not mix the for loops (iterator) with .GetNextFeature() (another iterator)**. 
