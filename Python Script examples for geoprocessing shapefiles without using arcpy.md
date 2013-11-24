from [Python Script examples for geoprocessing shapefiles without using arcpy](http://gis.stackexchange.com/questions/64146/python-script-examples-for-geoprocessing-shapefiles-without-using-arcpy/64158)


That's strange, as if people suddenly discovered the power of Python (without ArcPy which is just one Python module among others), see for example the question [Visualize shapefile in Python][1]:

 - **geospatial processing in Python  has a very long history, much older than Arcpy (or arcgisscripting)** -> no "mimic" the capabilities of ArcPy here, as Paul says, most were already there  before ArcPy. 
 - the reference for the Python modules is the **Python Package Index** (**Pypi**) and there is a dedicated section: [Topic :: Scientific/Engineering :: GIS][2]
 - you can do anything with these modules and it is often easier and faster than ArcPy because it is pure Python (no cursors...).
 - [Shapely][3] is one of these modules for processing geospatial geometries ->  calculate areas of a polygon and convert polygons to points..
 - if you want to process vector layers, there is [osgeo/ogr][4], [Fiona][5] or [Pyshp][6] (and others, less used) ->  query a shapefile by attributes, create new layer from selection, calculate areas of a polygon, convert polygons to points
 - for processing rasters, the standard is [osgeo/gdal][7]
 - for spatial analysis, there is [Pysal][8]
 - for 3D, you can use other Scientific modules like [numpy][9] or [scipy][10] (3D algorithms, grids, but also statistics, geostatistics, 2D or 3D) 
 - And I don't talk about [mapnik][11], [matplotlib/basemap][12],[Geodjango][13] and ...


You can combine all (Pysal with shapely, ...) and mix them with the other Scientific modules.

**Thus for Python Script examples, search for Pyshp Fiona, ogr, gdal or shapely in gis.stackexchange or the internet** (many examples, not only in English).)  
One of them in French (the scripts and the figures are universal !):  
 - [Python: Using vector and raster layers in a geological perspective, without GIS software][14]  
an other in English:  
 - [GIS with Python, Shapely, and Fiona][15]  
and in Spanish  
 - [Determination of areas of irregular polygons using the coordinates of the vertices ][16]  
in gis.stackexchange  
 - [Elevation profile 10 km each side of a line][17]  
 - [Updating Attributes using Pyshp][18]  
 - [How to create a 3D shapefile from a raster?][19]  
 - [Python Script for getting elevation difference between two points][20]  
 - etc

The script presented by Aaron can be written more simply with Fiona that uses only Python dictionaries:

```python
    import fiona
    with fiona.open('sites.shp', 'r') as input:
        with open('hw1a.txt', 'w') as output:
           for pt in input:
               id = pt['properties']['id']
               cover = pt['properties']['cover']
               x = str(point['geometry']['coordinates'][0])
               y = str(point['geometry']['coordinates'][21])
               output.write(id + ' ' + x + ' ' + y+ ' ' + cover + '\n')
```

and if you use shapely in addition:

```python
    from shapely.geometry import shape
    with fiona.open('sites.shp', 'r') as input:
        with open('hw1a.txt', 'w') as output:
           for pt in input:
               id = pt['properties']['id']
               cover = pt['properties']['cover']
               x = str(shape(pt['geometry']).x)
               y = str(shape(pt['geometry']).y)
               output.write(id + ' ' + x + ' ' + y+ ' ' + cover + '\n')
```

There are also two books: 

[Python Geospatial Development][22] of Eric Westra.

![enter image description here][23]

[Learning Geospatial Analysis with Python][27] of Joel Lawhead

![enter image description here][28]
        

Python is also used as a scripting language in other GIS applications like QGIS (Quantum GIS), GRASS GIS, gvSIG or OpenJump or 3D modelers like [Paraview][24] (and [Blender][25] also !). And you can use the majority of the geospatial modules in all these application (see [Visualising QGIS data with Blender][26])


  [1]: http://gis.stackexchange.com/questions/61862/visualize-shapefile-in-python/61868#61868
  [2]: https://pypi.python.org/pypi?:action=browse&show=all&c=391
  [3]: https://pypi.python.org/pypi/Shapely
  [4]: https://pypi.python.org/pypi/GDAL
  [5]: https://pypi.python.org/pypi/Fiona
  [6]: https://pypi.python.org/pypi/Fiona
  [7]: https://pypi.python.org/pypi/GDAL
  [8]: http://pythonhosted.org/PySAL/
  [9]: http://www.numpy.org/
  [10]: https://pypi.python.org/pypi/mayavi
  [11]: http://mapnik.org/
  [12]: http://matplotlib.org/basemap/
  [13]: http://geodjango.org/
  [14]: http://www.portailsig.org/content/python-utilisation-des-couches-vectorielles-et-matricielles-dans-une-perspective-geologique-
  [15]: http://macwright.org/2012/10/31/gis-with-python-shapely-fiona.html
  [16]: http://joseguerreroa.wordpress.com/2012/10/27/determinacion-de-areas-de-poligonos-irregulares-usando-las-coordenadas-de-sus-vertices-en-un-script-de-python/
  [17]: http://gis.stackexchange.com/questions/50108/elevation-profile-10-km-each-side-of-a-line/50841#50841
  [18]: http://gis.stackexchange.com/questions/57635/updating-attributes-using-pyshp/57696#57696
  [19]: http://gis.stackexchange.com/questions/56703/better-way-to-duplicate-a-layer-using-ogr-in-python/56722#56722
  [20]: http://gis.stackexchange.com/questions/59316/python-script-for-getting-elevation-difference-between-two-points/59322#59322
  [21]: http://gis.stackexchange.com/questions/61862/visualize-shapefile-in-python/61868#61868
  [22]: http://www.packtpub.com/python-geospatial-development/book
  [23]: http://i.stack.imgur.com/4yW9e.png
  [24]: http://www.paraview.org/
  [25]: http://www.blender.org/
  [26]: http://kodex.tumblr.com/post/37038839550/visualising-qgis-data-with-blender
  [27]:http://www.packtpub.com/learning-geospatial-analysis-with-python/book
  [28]:http://dgdsbygo8mp3h.cloudfront.net/sites/default/files/imagecache/productview_larger/1138OS_Cov.jpg
  
