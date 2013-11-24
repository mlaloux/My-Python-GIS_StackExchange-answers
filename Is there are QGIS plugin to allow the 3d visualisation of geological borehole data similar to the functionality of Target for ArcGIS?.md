
From [Is there are QGIS plugin to allow the 3d visualisation of geological borehole data similar to the functionality of Target for ArcGIS?](http://gis.stackexchange.com/questions/41701/is-there-are-qgis-plugin-to-allow-the-3d-visualisation-of-geological-borehole-da)


Yes, it is possible but using a Python script in the console 
see [For geologists: 3D geological boreholes ][1]

![3D surfaces in QGIS][2]
![boreholes in QGIS][3]

I presented the scripts in [visualizing 3D data (Z values) or data with z attribute: a solution ][4]
or [QGIS, représentation 3D des couches vectorielles (shapefiles dits 3D ou shapefiles avec attributs z) avec les modules Python Matplotlib ou Visvis à partir de la console Python][5] in French and [QGIS, visualización 3D de capas vectoriales con Python ][6] in Spanish.

More generally I use GRASS GIS to do, see [Automatic 3D geological boreholes representation (automate v.extrude from a table ?): my solution in Python ][7] and [3D geological volume modeling (raster 3D): is it really possible ?][8]

![boreholes][9] boreholes

![layer one][10] layer one

![layer 2][11] layer two

with interpolated surfaces and cross sections:
![enter image description here][12] 

or

![enter image description here][13]


  [1]: http://osgeo-org.1560.n6.nabble.com/For-geologists-3D-geological-boreholes-td5007085.html
  [2]: http://i.stack.imgur.com/ylHp8.jpg
  [3]: http://i.stack.imgur.com/Kx9Il.jpg
  [4]: http://osgeo-org.1560.n6.nabble.com/visualizing-3D-data-Z-values-or-data-with-z-attribute-a-solution-td5005360.html
  [5]: http://www.portailsig.org/content/qgis-representation-3d-des-couches-vectorielles-shapefiles-dits-3d-ou-shapefiles-avec-attrib
  [6]: http://geotux.tuxfamily.org/index.php/en/geo-blogs/item/315-qgis-visualizacion-3d-de-capas-vectoriales-con-python
  [7]: http://osgeo-org.1560.n6.nabble.com/Automatic-3D-geological-boreholes-representation-automate-v-extrude-from-a-table-my-solution-in-Pythn-td4978801.html
  [8]: http://osgeo-org.1560.n6.nabble.com/3D-geological-volume-modeling-raster-3D-is-it-really-possible-td4980148.html
  [9]: http://i.stack.imgur.com/doQjU.png
  [10]: http://i.stack.imgur.com/JYLUM.png
  [11]: http://i.stack.imgur.com/yiryI.png
  [12]: http://i.stack.imgur.com/r7tuL.jpg
  [13]: http://i.stack.imgur.com/Dq4Gm.jpg
