From [How to script map production in GRASS?](http://gis.stackexchange.com/questions/70945/how-to-script-map-production-in-grass)


For that, you need;

 1. to import the files (shapefiles, raster) in GRASS GIS
 2. use the adequate modules

You can use Bash or Python from:

 - the Command Console or the Python Shell of the Layer manager  

![enter image description here][1]

 - the GRASS shell (Python here, but you can also use [R][2])

![enter image description here][3]

 - the Mac shell  (Python, here, but you can also use [R][4]) 

![enter image description here][5]

 
To use bash scripts, see [Shell Scripting][6]  or [On scripting GRASS GIS: Building location-independent command line tools][7]  
To use Python, see [GRASS and Python][8] or [Python Scripts For GRASS GIS][9]  

I will develop Python that I know best.

**Python with grass module**

In Python, you simply access the GRASS functions as:

```python

    debligne =grass.read_command("v.to.db", flags="p", map="testgrass", type="line", option="start", units="meters" , quiet=True)
 ```
 
If you want to import all the tif files in a directory, see , for example, [Python: script to import multiple LANDSAT images to Grass GIS][10]

```python


    for dirpath, dirname, filenames in os.walk(dirpath):
        # Iterate through the files 
        for raster in filenames:
              # If the suffix is '.TIF', process
              if raster.upper().endswith('.tif'):
                    # full path to your file
                    full_path = os.path.join(dirpath, tif_file)
                    # GRASS commands
                    grass.message('Importing %s -> %s@%s...' % (full_path, tif_file, dirpath))
                    grass.run_command('r.in.gdal',flags = 'o',input = full_path, output = tif_file,quiet = True,overwrite = True)
```

See other example in the  above-mentioned references or in [Automatic 3D geological boreholes representation (automate v.extrude from a table ?): my solution in Python ][11] or [GRASS and the Python geospatial modules (Shapely, PySAL,...) ][12]

**Python with osgeo module**

You can also use the osgeo (GDAL/OGR) Python module:

```python


    from osgeo import ogr
    # open grass vector layer
    ds = ogr.Open('/Users/username/grassdata/geol/mymapset/vector/testgrass/head')
    # vector layer  
    layer = ds.GetLayer(0)
    layer.GetName()
    'testgrass'
     feat = layer.GetFeature(0) 
     # geometry
     geom = feat.GetGeometryRef() 
     geom.ExportToWkt() 
     'LINESTRING (186139.123704173340229 53082.654193976894021,188199.122798504744424 53467.758558732457459)'
```

  [1]: http://i.stack.imgur.com/pzWgb.png
  [2]: http://www.r-project.org/
  [3]: http://i.stack.imgur.com/TgTQ8.png
  [4]: http://www.r-project.org/
  [5]: http://i.stack.imgur.com/hRlph.jpg
  [6]: http://grasswiki.osgeo.org/wiki/Shell_scripting
  [7]: http://geoinformatics.fsv.cvut.cz/gwiki/On_scripting_GRASS_GIS:_Building_location-independent_command_line_tools
  [8]: http://grasswiki.osgeo.org/wiki/GRASS_and_Python
  [9]: http://code.google.com/p/postgis-grass-r-py/wiki/0003_01_PythonForGrassGis
  [10]: http://stackoverflow.com/questions/13073855/python-script-to-import-multiple-landsat-images-to-grass-gis
  [11]: http://osgeo-org.1560.x6.nabble.com/Automatic-3D-geological-boreholes-representation-automate-v-extrude-from-a-table-my-solution-in-Pythn-td4978801.html
  [12]: http://osgeo-org.1560.x6.nabble.com/GRASS-and-the-Python-geospatial-modules-Shapely-PySAL-td4985075.html
