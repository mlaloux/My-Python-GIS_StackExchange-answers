From [Get the vertices on a LineString either side of a Point](http://gis.stackexchange.com/questions/84512/get-the-vertices-on-a-linestring-either-side-of-a-point)


I don't understand your question:

```Python
    line = LineString([(0, 0), (2, 2)])
    # create a point which lies along the line
    point = line.interpolate(1)
    line.contains(point)
    True
```

![enter image description here][1]

You want the two lines which lie either side of the point ?

```Python

    line1 = LineString([line.coords[0],(point.x, point.y)])
    line2 = LineString([(point.x, point.y), line.coords[1]])
```

![enter image description here][2]

###Upgrade 1: with a line with multiple vertices

![enter image description here][3]

You need to iterate through the segments of the LineString to find the one that contains the point

The LineString must be iterate as pair to divide the line in segments.

```Python
    def pairs(lst):
        for i in range(1, len(lst)):
            yield lst[i-1], lst[i]

     line = LineString([(0,0),(1,2), (2, 2), (2,3), (4,2),(5,5)])

     for pair in pairs(list(line.coords)):
          if LineString([pair[0],pair[1]]).contains(point):
              print LineString([pair[0],pair[1]])

    LINESTRING (2.00 2.00, 2.00 3.00)
    
```

And you can use the previous answer: a rapid solution, for example ( this can be done better):

```Python
    line1 = []
    line2 = []
    cp = False
    for pair in pairs(list(line.coords)):
        if cp == False:
           line1.append(pair[0])
        if cp == True:
           line2.append(pair[1])
        if LineString([pair[0],pair[1]]).contains(point):
           line1.append((point.x,point.y))
           line2.append((point.x,point.y))
           line2.append(pair[1])
           cp = True
    line1 = LineString(line1)
    line2 = LineString(line2)
```

Result:

![enter image description here][4]
     


  [1]: http://i.stack.imgur.com/21aIy.jpg
  [2]: http://i.stack.imgur.com/bjlHd.jpg
  [3]: http://i.stack.imgur.com/y4btW.jpg
  [4]: http://i.stack.imgur.com/tu7LT.jpg
