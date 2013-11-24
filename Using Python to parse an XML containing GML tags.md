From [Using Python to parse an XML containing GML tags](http://gis.stackexchange.com/questions/58271/using-python-to-parse-an-xml-containing-gml-tags)

And in addition to the reply of sgillies, if you want the wkt format (wkt = "" ?), use his Shapely module:

```python
    from shapely.geometry.polygon import LinearRing

    linearing = []
    for polygon in geography.findall('{http://www.opengis.net/gml}Polygon'):
         for coord in polygon.findall("{http://www.opengis.net/gml}outerBoundaryIs/{http://www.opengis.net/gml}LinearRing/{http://www.opengis.net/gml}coord"):
             linearing.append((float(coord.findtext("{http://www.opengis.net/gml}X")),float(coord.findtext("{http://www.opengis.net/gml}Y"))))

    print LinearRing(a).wkt
    'LINEARRING (452847.6009000000194646 18596.0495999999984633, 415847.6009000000194646 184596.0495999999984633, 415847.6009000000194646 184596.0495999999984633, 452847.6009000000194646 18596.0495999999984633, 415847.6009000000194646 184596.0495999999984633, 415847.6009000000194646 184596.0495999999984633, 452847.6009000000194646 18596.0495999999984633)'

```
or with the two outerBoundaryIs: 

```python
    for outerBoundaryIs in geography.findall('{http://www.opengis.net/gml}Polygon/{http://www.opengis.net/gml}outerBoundaryIs'):
        linearing = []
        for coord in outerBoundaryIs.findall("{http://www.opengis.net/gml}LinearRing/{http://www.opengis.net/gml}coord"):
            linearing.append((float(coord.findtext("{http://www.opengis.net/gml}X")),float(coord.findtext("{http://www.opengis.net/gml}Y"))))         
        print LinearRing(linearing).wkt

    'LINEARRING (452847.6009000000194646 18596.0495999999984633, 415847.6009000000194646 184596.0495999999984633, 415847.6009000000194646 184596.0495999999984633, 452847.6009000000194646 18596.0495999999984633)'
    'LINEARRING (452847.6009000000194646 18596.0495999999984633, 415847.6009000000194646 184596.0495999999984633, 415847.6009000000194646 184596.0495999999984633, 452847.6009000000194646 18596.0495999999984633)'
```
and if you want the srsName of the polygon:

```python
    for polygon in geography.findall('{http://www.opengis.net/gml}Polygon'):
        polygon.attrib.get('srsName')
```

From Sean Gillies:
----

I enjoy using ElementTree. It's standardized in Python since 2.5 as xml.etree.ElementTree. Forgive me for being blunt, but you're using it wrong. I suggest trying the find, findtext, and findall methods when you know the structure of the data. Is Order your root element? If so, 

```python
    >>> geography = rootElement.find('OrderRequest/SiteGeography')
    >>> for polygon in geography.findall('{http://www.opengis.net/gml}Polygon'):
    ...     for coord in polygon.findall(
    ...             "{http://www.opengis.net/gml}outerBoundaryIs/"
    ...             "{http://www.opengis.net/gml}LinearRing/"
    ...             "{http://www.opengis.net/gml}coord"):
    ...         print(
    ...             coord.findtext("{http://www.opengis.net/gml}X"),
    ...             coord.findtext("{http://www.opengis.net/gml}Y"))
    ... 
    ('452847.6009', '18596.0496')
    ('415847.6009', '184596.0496')
    ('415847.6009', '184596.0496')
    ('452847.6009', '18596.0496')
    ('415847.6009', '184596.0496')
    ('415847.6009', '184596.0496')
```

http://pymotw.com/2/xml/etree/ElementTree/parse.html#finding-nodes-in-a-document has more advice on using ElementTree.

  






  [1]: https://pypi.python.org/pypi/Shapely/1.2.17


  






  [1]: https://pypi.python.org/pypi/Shapely/1.2.17
