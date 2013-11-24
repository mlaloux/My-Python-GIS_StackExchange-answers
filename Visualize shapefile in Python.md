From [Visualize shapefile in Python](http://gis.stackexchange.com/questions/61862/visualize-shapefile-in-python)


I do not know ArcPy, but I work with shapefiles and raster in Python for years

 1. For processing shapefiles in Python, there are many modules like [osgeo/ogr][1], [Fiona][2], [Pysal][3] or [Pyshp][4] ([shpUtils][5] is one of them and
not the most used), and others, see [Pypi: GIS][6] and examples on gis.stackexchange and many examples on the Web (not only in English). Most of them are much older than ArcPy (or arcgisscripting)...
 2. for processing raster you can use [osgeo/gdal][7], the standard
 3. For processing geospatial geometries, there is [shapely][8]
 4. For plotting the geometries you can use [matplotlib][9] and possibly [descartes][10], "extension" of matplotlib for areas, but also many, many other modules, see  [Pypi: Plotting][11] and modules like [mayavi][12] for 3D representation (matplotlib also)
 5. There are also modules like  [mapnik][13] which give you directly the possibilities of 1) read a shapefile and 4) plotting with the module [Pycairo][14].

After that, it's like a GIS:

 - you use the modules 1) to open, save the shapefiles and carry out the treatments with other modules like numpy or scipy, if you want.
 - you can use shapely for manipulation and analysis of the geometric objects (buffer, etc.).
 - you can use matplotlib to plot the geometries, but matplotlib do not know what you want to plot. It is your work with modules 1) or 3) to specify what to plot (attributes, etc,.) and how.

> If I want to visualise one certain column of my shapefile, how can I implement this in the code?

So, you must learn matplotib and the other modules. You have to learn ArcPy, it's the same...(there are lots of excellent tutorials on the web, especially for matplolib, and it's easier that ArcPy because it is pure Python).

Some examples with Python only

![enter image description here][15]

Geological map (polygon shapefile) with colors based on an attribute

![enter image description here][16]

3D Points (PointZ shapefile) with color based on an attribute 

![enter image description here][17]

3D points (Point shapefile with z as attribute) and 3D line (PolyLineZ shapefile) on a DEM, and on a raster draped onto the DEM surface.

![enter image description here][18]

Topographic profile  with z values and colors based on attributes (geological formations = Cross section) of the original shapefile (Polyline shapefile)

![enter image description here][19]

DEM (GeoTIFF) with the module Mayavi2

![enter image description here][20]

DEM (ESRI ascii grid, .asc) and Point shapefiles (with z as attribute) with the module [visvis][21]

![enter image description here][22]

Boreholes (3D buffer of a polylineZ with colors based on an attribute (geological formations), with a grid surface calculated with the modules numpy and matplotlib from a points shapefile (with z as an attribute), visualized with the module [visvis][23]


  [1]: https://pypi.python.org/pypi/GDAL
  [2]: https://pypi.python.org/pypi/Fiona
  [3]: https://pypi.python.org/pypi/PySAL/
  [4]: https://pypi.python.org/pypi/pyshp
  [5]: http://indiemaps.com/blog/2008/03/easy-shapefile-loading-in-python/
  [6]: https://pypi.python.org/pypi?:action=browse&show=all&c=391
  [7]: https://pypi.python.org/pypi/GDAL
  [8]: https://pypi.python.org/pypi/Shapely
  [9]: https://pypi.python.org/pypi/matplotlib
  [10]: https://pypi.python.org/pypi/descartes
  [11]: https://pypi.python.org/pypi?:action=search&term=plotting&submit=search
  [12]: https://pypi.python.org/pypi/mayavi
  [13]: http://mapnik.org/
  [14]: http://cairographics.org/pycairo/
  [15]: http://i.stack.imgur.com/hm9DT.jpg
  [16]: http://i.stack.imgur.com/Rqiav.jpg
  [17]: http://i.stack.imgur.com/b6NWR.jpg
  [18]: http://i.stack.imgur.com/CoZQn.jpg
  [19]: http://i.stack.imgur.com/9olre.png
  [20]: http://i.stack.imgur.com/FYEXG.png
  [21]: http://code.google.com/p/visvis/
  [22]: http://i.stack.imgur.com/nXZJD.jpg
  [23]: http://code.google.com/p/visvis/
