From [How to call processing toolbox in Qgis' python console](http://gis.stackexchange.com/questions/75666/how-to-call-processing-toolbox-in-qgis-python-console)


To learn the processing module in Python, the easiest solution for me is:

 1. execute the Processing command (from the Toolbox), for example v.to.rast.val
 2. examine the "processing_qgis.log" file in the ".qgis2/processing/" folder

where you can examine all the running algorithms:

     ALGORITHM|Mon Oct 28 2013  
     12:30:34|processing.runalg("grass:v.to.rast.value","/Users/Shared/polygon.shp",0,1,"202563.92575,204206.182887,89106.9864984,89783.2100251",0,-1,0.0001,"/Users/Shared/polyraster.tif")

The actual commands are in:

 - "grass_batch_job.sh" or "grass_batch_job.bat" files (in the ".qgis2/processing/" folder) for GRASS GIS;
 - "saga_batch_job.sh" or "saga_batch_job.bat" files for SAGA GIS; 
 - "processing_script.r" file for R.
 

So, in the Python console:

```python
    import processing
    processing.runalg("grass:v.to.rast.value","/Users/Shared/polygon.shp",0,1,"202563.92575,204206.182887,89106.9864984,89783.2100251",0,-1,0.0001,"/Users/Shared/polyraster.tif")
    processing.runalg("grass:r.statistics","/Users/Shared/polyraster.tif","/Users/Shared/mydem.asc",1,True,"202086.577,205625.414407,88411.048,90534.3504441",0,"/Users/Shared/test.tif")
```
You can also use one of the displayed layers:

```python
    vectorlayer = qgis.utils.iface.mapCanvas().currentLayer().source()
    processing.runalg("grass:v.to.rast.value",vectorlayer,0,1,"202563.92575,204206.182887,89106.9864984,89783.2100251",0,-1,0.0001,"/Users/Shared/polyraster.tif")
```
    
but before, you need to understand all the parameters of the command [r.statistics][1] and the problems as [r.statistics limitation to CELL][2]


  [1]: http://grass.osgeo.org/grass64/manuals/r.statistics.html
  [2]: http://osgeo-org.1560.x6.nabble.com/r-statistics-limitation-to-CELL-td4017639.html
