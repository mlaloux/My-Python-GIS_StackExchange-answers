from [Python Script for getting elevation difference between two points](http://gis.stackexchange.com/questions/59316/python-script-for-getting-elevation-difference-between-two-points/59322)

As a geologist, I often use this technique to make geological cross section in pure Python. I presented a complete solution in [Python: Using vector and raster layers in a geological perspective, without GIS software][1] (in French)

I present  here  a summary in English:

 - to show you how to extract the elevation values ​​of a DEM
 - how to treat these values

If you open a DEM with GDAL/OGR Python module:

```python
    from osgeo import gdal
    # raster dem10m
    file = 'dem10m.asc'
    layer = gdal.Open(file)
    gt =layer.GetGeoTransform()
    bands = layer.RasterCount
    print bands
    1
    print gt
    (263104.72544800001, 10.002079999999999, 0.0, 155223.647811, 0.0, -10.002079999999999)
```
 
As a result, you have the number of bands and the geotransform parameters. If you want to extract the value of the raster under a xy point: 

```python
    x,y  = (263220.5,155110.6)
    # transform to raster point coordinates
    rasterx = int((x - gt[0]) / gt[1])
    rastery = int((y - gt[3]) / gt[5])
    # only one band here
    print layer.GetRasterBand(1).ReadAsArray(rasterx,rasterx, 1, 1)
    array([[222]]) 
```

As it is a DEM, you get the elevation value under the point. With 3 raster bands with the same xy point you get 3 values (R,G,B). So you could make a function that allows to get the values of multiple rasters under a xy point:

```python
    def Val_raster(x,y,layer,bands,gt):
        col=[]
        px = int((x - gt[0]) / gt[1])
        py =int((y - gt[3]) / gt[5])
        for j in range(bands):
            band = layer.GetRasterBand(j+1)
            data = band.ReadAsArray(px,py, 1, 1)
            col.append(data[0][0])
      return col
```

application

```python
    # with a DEM (1 band)
    px1 = int((x - gt1[0]) / gt1[1])
    py1 = int((y - gt1[3]) / gt1[5])
    print Val_raster(x,y,layer, band,gt)
    [222] # elevation
    # with a geological map (3 bands)
    px2 = int((x - gt2[0]) / gt2[1])
    py2 = int((y - gt2[3]) / gt2[5])
    print Val_raster(x,y,couche2, bandes2,gt2)
    [253, 215, 118] # RGB color  
```

After that, you process the line profile (which may have segments):

```python
    # creation of an empty ogr linestring to handle all possible segments of a line with  Union (combining the segements)
    profilogr = ogr.Geometry(ogr.wkbLineString)
    # open the profile shapefile
    source = ogr.Open('profilline.shp')
    cshp = source.GetLayer()
    # union the segments of the line
    for element in cshp:
       geom =element.GetGeometryRef()
       profilogr = profilogr.Union(geom)
```

To generate equidistant points on the line, you can use the [Shapely][2] module with interpolate (easier than ogr)

```python
    from shapely.wkb import loads
    # transformation in Shapely geometry
    profilshp = loads(profilogr.ExportToWkb())
    # creation the equidistant points on the line with a step of 20m
    lenght=profilshp.length
    x = []
    y = []
    z = []
    # distance of the topographic profile
    dista = []
    for currentdistance  in range(0,lenght,20):
         # creation of the point on the line
         point = profilshp.interpolate(currentdistance)
         xp,yp=point.x, point.y
         x.append(xp)
         y.append(yp)
         # extraction of the elevation value from the MNT
         z.append(Val_raster(xp,yp,layer, bands,gt)[0]
         dista.append(currentdistance)
```

and the results (with also the RGB values of a geologic map) with the x,y,z, distance values of the lists
In 3D with [matplotlib][3] and [Visvis][4] (x,y,z values) 

![enter image description here][5]

Cross sections (x, elevation from currentdistance (dista list)) with [matplotlib][3]:
![enter image description here][6]

 ![enter image description here][7]
        


  [1]: http://www.portailsig.org/content/python-utilisation-des-couches-vectorielles-et-matricielles-dans-une-perspective-geologique-
  [2]: https://pypi.python.org/pypi/Shapely/1.2.17
  [3]: https://pypi.python.org/pypi/matplotlib/1.2.1
  [4]: https://pypi.python.org/pypi/visvis/1.8
  [5]: http://i.stack.imgur.com/fsTaZ.jpg
  [6]: http://i.stack.imgur.com/Bu6SE.jpg
  [7]: http://i.stack.imgur.com/xRrbC.jpg


