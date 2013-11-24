From [How to use Z-Coordinate in QGIS?](http://gis.stackexchange.com/questions/43342/how-to-use-z-coordinate-in-qgis)


see [Is there are QGIS plugin to allow the 3d visualisation of geological borehole data similar to the functionality of Target for ArcGIS?][1] for my solution in Python with the modules Matplotlib and [Visvis][2]

The Interpolation plugin generates surfaces in ArcInfo ASCII grid format (. asc) but it offers no way to visualize the results in 3D or to process the 3d shapefiles (PointZ,..)

I presented a solution and the scripts in the QGIS-Developer list [visualizing 3D data (Z values) or data with z attribute: a solution ][3]
and, in French, in [QGIS, représentation 3D des couches vectorielles (shapefiles dits 3D ou shapefiles avec attributs z) avec les modules Python Matplotlib ou Visvis à partir de la console Python][4] or in Spanish in [QGIS, visualización 3D de capas vectoriales con Python ][5] 

**3d visualisation of the points** (Visvis)

![points][6] 

**a resulting 3d grid** (here with matplolib function **griddata** but you can use all the interpolation algorithms of Scipy or others modules)

![grid][7]

**The shaded surface** with an elevation colormap (Visvis)

![surfaces][8]

**3d contour lines (Matplotlib)**
![enter image description here][9]

**and for fun ...(Visvis)**

![enter image description here][10]


But build a plugin seems a priori to me very complex and time consuming:

- plot 3 d points is easy.
- but in all other cases, all the scenarios (3D shapefile points, lines or polygons, shapefile with z attribute, interpolation algorithm, ...)  should be considered before.
- You need Python modules that are not present by default in QGIS (like Shapely, Scipy or Visvis)


  [1]: http://gis.stackexchange.com/questions/41701/is-there-are-qgis-plugin-to-allow-the-3d-visualisation-of-geological-borehole-da
  [2]: http://code.google.com/p/visvis/
  [3]: http://osgeo-org.1560.n6.nabble.com/visualizing-3D-data-Z-values-or-data-with-z-attribute-a-solution-td5005360.html
  [4]: http://www.portailsig.org/content/qgis-representation-3d-des-couches-vectorielles-shapefiles-dits-3d-ou-shapefiles-avec-attrib
  [5]: http://geotux.tuxfamily.org/index.php/en/geo-blogs/item/315-qgis-visualizacion-3d-de-capas-vectoriales-con-python
  [6]: http://i.stack.imgur.com/AtIdL.png
  [7]: http://i.stack.imgur.com/43dng.png
  [8]: http://i.stack.imgur.com/GK4kV.png
  [9]: http://i.stack.imgur.com/TQI5Y.png
  [10]: http://i.stack.imgur.com/q3mf7.gif
