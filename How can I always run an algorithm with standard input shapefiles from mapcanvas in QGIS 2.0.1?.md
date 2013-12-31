From [How can I always run an algorithm with standard input shapefiles from mapcanvas in QGIS 2.0.1?](http://gis.stackexchange.com/questions/78758/how-can-i-always-run-an-algorithm-with-standard-input-shapefiles-from-mapcanvas/)


Change you script in:


```python


    ##difference_between_pst/astenot=name
    ##output_alg0=output vector
    ##output_alg1=output vector
    ##output_layer_alg2=output vector
    ##output_layer_alg3=output vector
    vectorlayer_1 = "pst"
    vectorlayer_2 = "astenot"
    outputs_0=Processing.runalg("qgis:difference", vectorlayer_1, vectorlayer_2,   output_alg0)
    outputs_1=Processing.runalg("qgis:difference", vectorlayer_2, vectorlayer_1,  output_alg1)
    outputs_2=Processing.runalg("qgis:saveselectedfeatures", outputs_0['OUTPUT'], output_layer_alg2)
    outputs_3=Processing.runalg("qgis:saveselectedfeatures", outputs_1['OUTPUT'], output_layer_alg3)


![enter image description here][1]

``` 


  [1]: http://i.stack.imgur.com/qGvWi.jpg
