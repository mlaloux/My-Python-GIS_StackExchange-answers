From [How to terminate Python scripts in Processing framework properly?](http://gis.stackexchange.com/questions/81530/how-to-terminate-python-scripts-in-processing-framework-properly)

- first thing, `osgeo.gdal` is for pure Python scripting:

```python
    from osgeo import gdal  
    raster  = gdal.Open('your.tif')   
    raster.GetProjection()
```

Everything else is a problem of osgeo, and not of PyQGIS, since you use 2 QGIS existing layers:

```python
     ##raster_1=raster  
     ##raster_2=raster
```

you don't need it. To process the layers, the correct code is:

```python
    raster_1 = processing.getobject(raster_1)   
    raster_2 = processing.getobject(raster_2)
```

an the crs is given by:

```python

    new_proj = raster.crs()
```

- second thing, you cannot load a raster in QGIS without projection (if not yet existing, it is chosen by QGIS, according to some criteria):

![enter image description here][1]

So your first condition `if proj is None:`,  is unnecessary and your script become (you don't need a list with only two layers):

```python
    raster_1 = processing.getobject(raster_1)
    raster_2 = processing.getobject(raster_2)
    if raster_1.crs()==raster_2.crs():
         action
    else:
         WrongCRS()
```

and the script terminate  by himself. 

PS:
in the last version of the processing module, it is now:

```python
    processing.getObjects()
```
PS2:

For terminate the script, you can embed your script in a function and use `sys.exitfunc()`
(I use the Python console to to see the results)

example:

```python

    import atexit
    .....
    def all_done():
        message = '- Rasters must have the same CRS!\n\nExecution cancelled!'
        print message   
    atexit.register(all_done)
    raster_1 = processing.getObject(raster_1)
    raster_2 = processing.getObject(raster_2)
    def main():
        if raster_1.crs() != raster_2.crs():
             sys.exitfunc()
        else:
              message = "ok"
              print message
              continue
    main()
```

Result in the Python console:

![enter image description here][2]


  [1]: http://i.stack.imgur.com/ioUad.jpg
  [2]: http://i.stack.imgur.com/tWuJK.jpg
