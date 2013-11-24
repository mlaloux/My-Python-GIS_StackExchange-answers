From [OGR Projection transformation error](http://gis.stackexchange.com/questions/61823/ogr-projection-transformation-error/61830)


For a complete history of the evolution from EPSG:900913 to EPSG 3785 and finally EPSG:3857, look at [Análisis de Google Maps][1] (in Spanish) and the conclusions are:

 - The projection parameters defined for the EPSG: 900913 or 3785 describe a Mercator projection with  geographic coordinates defined on a spherical model of the Earth, 6378.137m ratio, as specified in the following line (see [EPSG:3785][2]) :

    SPHEROID["**Popular Visualisation Sphere", 6378137,** 0, AUTHORITY["EPSG","7059"]]	

 - The official EPSG: 3857, in contrast, suggests a Mercator projection with coordinates defined on the WGS84 ellipsoid with a flattening (f) of 1/298.257223563:

    SPHEROID["**WGS 84", 6378137, 298.257223563**, AUTHORITY["EPSG","7030"]]

And the prj of the shapefile is EPSG 3857 and not EPSG 900913/3785 witch is ([3785 ESRI .prj][3]):

> PROJCS["Popular Visualisation CRS / Mercator",GEOGCS["Popular Visualisation CRS",DATUM["D_Popular_Visualisation_Datum",SPHEROID["Popular_Visualisation_Sphere",6378137,0]],PRIMEM["Greenwich",0],UNIT["Degree",0.017453292519943295]],PROJECTION["Mercator"],PARAMETER["central_meridian",0],PARAMETER["scale_factor",1],PARAMETER["false_easting",0],PARAMETER["false_northing",0],UNIT["Meter",1]]

If I try with the definitions of the module osr, the transformation works:

 
 ```python
    from osgeo import osr
    x,y = (6000,5000)
    p3857 = osr.SpatialReference()
    p3857.ImportFromEPSG(3857)
    p900913 = osr.SpatialReference()
    p900913.ImportFromEPSG(900913)
    transformation = osr.CoordinateTransformation(p900913,p3857)
    x2,y2 = transformation.TransformPoint(x, y)
    print "%.3f, %.3f" % (x2,y2)
    6000.000, 5000.000
 
 ```

and

 
 ```python

    p3857.ExportToWkt()   
    'PROJCS["WGS_84_Pseudo_Mercator",GEOGCS["GCS_WGS_1984",DATUM["D_WGS_1984",
    SPHEROID["WGS_1984",6378137,298.257223563]],PRIMEM["Greenwich",0],UNIT["Degree",   
    0.017453292519943295]],PROJECTION["Mercator"],PARAMETER["central_meridian",0],
    PARAMETER["false_easting",0],PARAMETER["false_northing",0],UNIT["Meter",1],
    PARAMETER["standard_parallel_1",0.0]]'
    
    p900913.ExportToWkt()
    'PROJCS["Google Maps Global Mercator",GEOGCS["WGS84",DATUM["WGS_1984",
    SPHEROID["WG84",6378137,298.257223563,AUTHORITY["EPSG","7030"]],
    AUTHORITY["EPSG","6326"]],PRIMEM["Greenwich",0,AUTHORITY["EPSG","8901"],
    UNIT["degree",0.01745329251994328,AUTHORITY["EPSG","9122"]],
    AUTHORITY["EPSG","4326"]],PROJECTION["Mercator_2SP"],
    PARAMETER["standard_parallel_1",0],PARAMETER["latitude_of_origin",0],
    PARAMETER["central_meridian",0],,PARAMETER["false_easting",0],
    PARAMETER["false_northing",0],UNIT["Meter",1],EXTENSION["PROJ4",
    "+proj=merc +a=6378137 +b=6378137 +lat_ts=0.0 +lon_0=0.0 +x_0=0.0 +y_0=0 +k=1.0 
    +units=m  +nadgrids=@null +wktext  +no_defs"],AUTHORITY["EPSG","900913"]]'
 
 ```

But if you use the WKT provided by your .prj file, the result is:

 
 ```python

    p = osr.SpatialReference()
    p.ImportFromWkt(yourwkt)
    p.ExportToProj4()
    ERROR 6: No translation for Mercator_Auxiliary_Sphere to PROJ.4 format is known.
 
 ```


So, it is the prj from FME that is the problem and not that of QGIS.


  [1]: http://www.ign.gob.ar/argenmap/argenmap.jquery/docs/analisisdegooglemaps.html
  [2]: http://spatialreference.org/ref/epsg/3785/html/
  [3]: http://spatialreference.org/ref/epsg/3785/esriwkt/
