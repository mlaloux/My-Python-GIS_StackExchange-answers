From [Display a georeferenced DEM surface in 3D matplotlib](http://gis.stackexchange.com/questions/66367/display-a-georeferenced-dem-surface-in-3d-matplotlib)

Matplotlib knows nothing about georeferenced surfaces, it only knows x,y,z coordinates. You can also use [Visvis][1] or [Mayavi][2].

 - the original DEM  

![enter image description here][3]

 - you must first extract the x,y, z coordinates of the grid ( a raster is a grid of pixels and with a DEM, the value of the pixel is the elevation, z) with osgeo.gdal. No script here because  it is possible to find the solutions on Gis StackExchange or on the web.

 - after, you can plot the points in 3D

```Python
        from mpl_toolkits.mplot3d.axes3d import *
        import matplotlib.pyplot as plt
        from matplotlib import cm
        fig = plt.figure()
        ax = Axes3D(fig)
        ax.scatter3D(x,y,z,c=z,cmap=plt.cm.jet)  
        plt.show()
```

![enter image description here][4]

 - and you must reconstruct a 3D grid (surface) with the function griddata of matplotlib (Delaunay)

```Python
        import numpy as np
        from matplotlib.mlab import griddata
        # craation of a 2D grid
        xi = np.linspace(min(x), max(x))
        yi = np.linspace(min(y), max(y))
        X, Y = np.meshgrid(xi, yi)
        # interpolation
        Z = griddata(x, y, z, xi, yi)
        fig = plt.figure()
        ax = Axes3D(fig)
        ax.plot_surface(X, Y, Z, rstride=1, cstride=1, cmap=cm.jet,linewidth=1, antialiased=True)
        plt.show()
```
The grid (with visvis):

![enter image description here][5]!

The coloured grid (with matplotlib):

![enter image description here][6]

 - but you can also use others interpolation algorithms (Scipy thin-plate spline here) and drape colours

```Python
        import scipy as sp
        import scipy.interpolate
        # 2D grid construction
        spline = sp.interpolate.Rbf(x,y,z,function='thin-plate')
        xi = np.linspace(min(x), max(x))
        yi = np.linspace(min(y), max(y))
        X, Y = np.meshgrid(xi, yi)
        # interpolation
        Z = spline(X,Y)
```

![enter image description here][7]

With [Visvis][8], you can make animations:

![enter image description here][9]

You can even plot the 3D contours:

![enter image description here][10]

 - Comparison between a projected DEM (with GRASS GIS, x 1) and the equivalent non projected DEM (x,y,z, with Visvis x 1)



![enter image description here][11] ![enter image description here][12]


  [1]: http://code.google.com/p/visvis/
  [2]: http://code.enthought.com/projects/mayavi/
  [3]: http://i.stack.imgur.com/TOfgK.jpg
  [4]: http://i.stack.imgur.com/CVTA4.jpg
  [5]: http://i.stack.imgur.com/QybsO.jpg
  [6]: http://i.stack.imgur.com/ZanzF.jpg
  [7]: http://i.stack.imgur.com/Bdj00.jpg
  [8]: http://code.google.com/p/visvis/
  [9]: http://i.stack.imgur.com/BZYZH.gif
  [10]: http://i.stack.imgur.com/9RfoP.jpg
  [11]: http://i.stack.imgur.com/40dOl.jpg
  [12]: http://i.stack.imgur.com/asW5r.jpg
