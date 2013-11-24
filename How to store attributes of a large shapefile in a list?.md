
from [How to store attributes of a large shapefile in a list?](http://gis.stackexchange.com/questions/61935/how-to-store-attributes-of-a-large-shapefile-in-a-list/61968)


It is not a problem of numpy arrays or other things, it's a problem of garbage collection. Each time you create a list, everything is stored in memory and if the list is big.... So you need to know Python (and not only PyQGIS) and the tricks to get out ("big data problem", you should reduce memory usage and use algorithms that do not slow down the process):

**First: do not use a while loop (see [While loop][1]):**

> "**While loops**, like the For Loop, are used for repeating sections of code - 
    but unlike a for loop, the while loop will not run n times, 
    but until a defined condition is met. As the for loop in Python is so powerful, 
    while **is rarely used, except in cases where a user's input is required**" 

which is not the case here and it slows things due to first testing the True condition every loop.  

If you want to process all data in a layer in QGIS 1.8 (not necessary in master version which allows any process, even if the elements of a layer are not selected).

 ```python
    def select_all(layer):
        # select all elements of a layer, geometry and attributes
        layer.select([])
        layer.setSelectedFeatures([obj.id() for obj in layer])

    layer = qgis.utils.iface.activeLayer()
    select_all(layer)
 ```
And after

```python

    for elem in layer.selectedFeatures():
        geom= elem.geometry()
        attrs = elem.attributeMap()
 ```

and no more while loop !

**Secondly, if you only need the values of a dictionary, uses attrs.values() and  not  attrs.iteritems(), forcing the loop to go through all keys and values**

 ```python
    addA = []
    for elem in layer.selectedFeatures():
        attrs = elem.attributeMap()
        for  value in attrs.values():
            addA.append(value.toString())
 ```

**Thirdly, you can use iterators, generators or list comprehension**, see [Python Generator Hacking][3]:

List comprehension (can be used anywhere a sequence is expected)

what it means: 

 ```python
    result = []
    for x in s:
    if condition:
        result.append(expression)
 ```

so:

```python
    addA = []
    for elem in layer.selectedFeatures():
        attrs = elem.attributeMap()
        addA.append([value.toString() for value in attrs.values()])
```

**and if you want, you can construct a nested list comprehension for addA in one line (without using a preliminary empty list, saving memory):**

```python
    addA = [[value.toString() for value in elem.attributeMap().values()] for elem in macouche.selectedFeatures()]
```

> "This means that list comprehensions aren’t useful if you’re working with iterators that return an infinite stream or a very large amount of data. Generator expressions are preferable in these situations" ([functional Python][4])

with Generators:

> A generator is a one-time operation. It does not construct a list and you can iterate over the generated data once, but if you want to do it again, you have to call the generator function again. Once consumed, it disappears from memory.

what it means (no list, return one value): 

 ```python

    for x in s: 
       if condition:
           yield expression
 ```

so:

```python

    addA = []
    for elem in layer.selectedFeatures():
         attrs = elem.attributeMap()
         generator =(value.toString() for value in attrs.values())
         print generator
 
     <generator object <genexpr> at 0x12bff6eb0>
      ....
 ```

Its purpose is only iteration:

```python

    addA = []
    for elem in layer.selectedFeatures():
        attrs = elem.attributeMap()
        generator =(value.toString() for value in attrs.values())
        for i in generator:
            addA.append(i)
 ```

and with a huge savings in memory area:

 ```python

    addA = []
    # generator
    for i in ((value.toString() for value in elem.attributeMap().values()) for elem in  macouche.selectedFeatures()):
        for j in i:
             addA.append(j)
 ```

I hope this will help



  [1]: http://wiki.python.org/moin/WhileLoop
  [2]: http://docs.python.org/2/howto/functional.html
  [3]: http://www.dabeaz.com/usenix2009/generators/GeneratorUSENIX.pdf
  [4]: http://docs.python.org/2/howto/functional.html
