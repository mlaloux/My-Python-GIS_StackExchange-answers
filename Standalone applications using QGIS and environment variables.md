
From [Standalone applications using QGIS and environment variables](http://gis.stackexchange.com/questions/77660/standalone-applications-using-qgis-and-environment-variables)

Consulting the PyQGis documentation, you’ll find that there are four main ways to utilize the PyQGis API: 

 1. via commands in the Python console
 2. via Python scripts in Processing or with the [ScritRunner][1] plugin of Gary Sherman
 3. via development of custom plugins to the QGis application
 4. outside QGIS (in the Python shell or  creating an applications with PyQt4 and not Tkinter (why import Tkinter twice ?)

and you are interested in this last point:

 - you can use PyQGIS as any other Python module. But Python does not know where to find PyQGIS. For that, you need to add the PyQGIS folder to the PYTHONPATH (for Windows, look at [How to add to the pythonpath in windows 7?][2]). 

> Certainly ArcPy doesn't require people to mess around with the computer's environmental settings, so I'm having difficulty understanding why PyQGIS does

Because you use the Python version of ArcGIS, in other cases, the same is true, look [using arcpy outside arcmap][3] or [Configure PyScripter to use with QGIS (and still use arcpy) on Windows][4], for example.

You don't need here PyQt4, Tkinter or  qgis.gui:

```Python

    from qgis.core import *
    QgsApplication.setPrefixPath("yourpath", True)
    QgsApplication.initQgis()
    # or your solution
    # read a shapefile 
    layer = QgsVectorLayer('your.shp', 'your', 'ogr')
    layer.isValid()
    True
    # loop through layer 
    for elem in layer.getFeatures():
        geom= elem.geometry()
        attr =elem.attributes()
        (processing)

     # interaction with other Python module: Shapely, for example
     from shapely.geometry import shape
     from json import loads
     for elem in layer.getFeatures():
           shapely_geometry = shape(loads(elem.geometry().exportToGeoJSON()))
```

 - you can create an application. You need here PyQt4 (and not Tkinter) and qgis.gui. 

> I would like to program a standalone application using PyQGIS the way I can already program standalone applications using ArcPy.

So, for that, you must learn PyQt4 , as you have to learn Tkinter (or wxPython), for example. This is another problem: the solution given by gsherman is a problem of PyQt4, not of PyQGIS (look at [PyQt4 tutorial][5], for example)



     


  [1]: http://plugins.qgis.org/plugins/scriptrunner/
  [2]: http://stackoverflow.com/questions/3701646/how-to-add-to-the-pythonpath-in-windows-7
  [3]: http://gis.stackexchange.com/questions/18781/using-arcpy-outside-arcmap
  [4]: http://mapoholic.wordpress.com/2012/06/28/configure-pyscripter-qgis/
  [5]: http://zetcode.com/gui/pyqt4/
