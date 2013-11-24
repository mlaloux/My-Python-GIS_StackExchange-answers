from [Is it possible to open rasters as array in NumPy without using another library?](http://gis.stackexchange.com/questions/76919/is-it-possible-to-open-rasters-as-array-in-numpy-without-using-another-library)


Numpy is made for processing arrays and not for reading image files. You need other modules to read the raster and convert it to an array.

If you do not want use GDAL or ArcPy:

  - Numpy use Scipy for that: [Image manipulation and processing using Numpy and Scipy][1]

```python

    from scipy import misc
    raster = misc.imread('image.tif')
    type(raster)
    <type 'numpy.ndarray'>
```


from [Is it possible to open rasters as array in NumPy without using another library?](http://gis.stackexchange.com/questions/76919/is-it-possible-to-open-rasters-as-array-in-numpy-without-using-another-library/76937)

  - but you can also use the [Python Image Library or PIL][2], forked in [Pillow][3]: see [Python for graphics][4]

```python

    import Image
    import numpy as np
    raster =Image.open('image.tif')
    print raster.format, raster.size, raster.mode, raster.info
    TIFF (330, 440) P {'compression': 'raw', 'dpi': (300, 300)   
    imarray=np.array(raster)
    type(imarray)
    <type 'numpy.ndarray'>
```

 - you can also use [matplotlib][7], only png file natively, using PIL for the others 

```python
 

    import matplotlib.pyplot as plt
    imarray = plt.imread('image.tif')
    type(imarray)
    <type 'numpy.ndarray'>


```

  - and a lot of other modules as [OpenCV][5]: see [Numpy Tips and Tricks][6]

```python

    import cv2
    im = cv2.imread("image.tif")
    type(im)
    <type 'numpy.ndarray'>
```


If you use Python 3.3 you can use [Pillow][8] or the last version of Scipy (> 0.12)

![enter image description here][9]

But you have no information about the georeferencing parameters of the raster

```python

    from osgeo import gdal
    raster = gdal.Open("image.tif")
    imarray = np.array(raster.ReadAsArray())
    type(imarray)
    <type 'numpy.ndarray'>
    # georeferencing parameters 
    geotransform = raster.GetGeoTransform()
    print geotransform
    (162012.67788132755, 1.00078911763392, 0.0, 108172.86938540942, 0.0, -1.00078911763392)
```



  [1]: http://scipy-lectures.github.io/advanced/image_processing/
  [2]: http://effbot.org/zone/pil-changes-116.htm
  [3]: https://pypi.python.org/pypi/Pillow/2.2.1
  [4]: http://dmr.ath.cx/gfx/python/
  [5]: http://opencv.org/#numpy-and-opencv
  [6]: http://jayrambhia.com/blog/numpy-tricks/
  [7]: http://matplotlib.org/users/image_tutorial.html
  [8]: https://pypi.python.org/pypi/Pillow/2.2.1
  [9]: http://i.stack.imgur.com/cW0Bo.jpg
