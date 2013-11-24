From [Problem with converting rasters to arrays and adding them to main array, then converting to raster via NumPyArrayToRaster : output is NoData values](http://gis.stackexchange.com/questions/72336/problem-with-converting-rasters-to-arrays-and-adding-them-to-main-array-then-co)


 Some points need to be clarified:

 - Are the rasters single-band or multi-bands ?
 - what is the shape of your main_array ?
 - What is the final result of your main_array ?
 
Example with numpy 
-------------

Create an array filled with zeros:

```python
    import numpy as np
    main_array= np.zeros((4,5),dtype=np.float64) 
    # In your case:
    # main_array = np.zeros(template_array.shape, dtype=np.float64)
    print main_array
    array([[ 0.,  0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.,  0.]])
    main_array.shape
    (4, 5)
    main_array.size
    20
```


Sums of arrays:

```python

    newarray = np.array([[ 1.,  2.,  3.,  4.,  5.],
       [ 1.,  2.,  3.,  4.,  5.],
       [1.,  2.,  3.,  4.,  5.],
       [1.,  2.,  3.,  4.,  5.]])

    main_array = main_array + newarray
    print main_array
    array([[ 1.,  2.,  3.,  4.,  5.],
       [ 1.,  2.,  3.,  4.,  5.],
       [ 1.,  2.,  3.,  4.,  5.],
       [ 1.,  2.,  3.,  4.,  5.]])
    main_array = main_array + newarray
    print main_array
    array([[  2.,   4.,   6.,   8.,  10.],
       [  2.,   4.,   6.,   8.,  10.],
       [  2.,   4.,   6.,   8.,  10.],
       [  2.,   4.,   6.,   8.,  10.]])
     etc.
```

I don't use ArcPy but with GDAL/OGR the result is not NULL:
```python
    import numpy as np
    from osgeo import gdal
    ds = gdal.Open('myraster')
    array = np.array(ds.GetRasterBand(1).ReadAsArray())
    array.shape
    (62, 90)
    main_array = np.zeros(array.shape, dtype=np.float64)
    main_array.shape
    (62, 90)
    main_array + array == array
    array([[ True,  True,  True, ...,  True,  True,  True],
       [ True,  True,  True, ...,  True,  True,  True],
       [ True,  True,  True, ...,  True,  True,  True],
       ..., 
       [ True,  True,  True, ...,  True,  True,  True],
       [ True,  True,  True, ...,  True,  True,  True],
       [ True,  True,  True, ...,  True,  True,  True]], dtype=bool)
```python

    

