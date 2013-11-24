From [Why is SQLite python command not working?](http://gis.stackexchange.com/questions/43590/why-is-sqlite-python-command-not-working)

First, the name index  is reserved by SQLite ( [SQLite: create Index][1] ). 

Then you need to review the string formatting in Python ( see for example [Python String Format][2] )

What you want is, in SQL (index is renamed ind):

    'INSERT INTO bbi_catalog_aug2012 (ind,path,filename) VALUES ("...","...","....");'

In Python with the older "%" string formatter:

```python
    statement = "INSERT INTO %s (%s) VALUES (%s,%s,%s);" %(table,columns,1,2,3)
```
or with the new string.format()
```python
     statement = "INSERT INTO {0} ({1}) VALUES ({2},{3},{4});".format(table,columns,1,2,3)
```
and the result is:
```python
    'INSERT INTO bbi_catalog_aug2012 (ind,path,filename) VALUES (1,2,3);'
```
So your script:

```python
    conn = sqlite.connect("ngi.sqlite")
    with conn:
        cur = conn.cursor()
         for root, dirnames, filenames in os.walk(basedir):
          print 'root is ' + root
          for filename in filenames:
              ext = os.path.splitext(filename)[1].lower()
              if ext == extension:
                     index = filename[:9]
                     filebase = os.path.splitext(filename)[0]
                     fullpath = os.path.join(root,filename)
                     statement = "INSERT INTO {0} ({1}) VALUES ('{2}','{3}','{4}');".format(table,columns,index,fullpath,filebase)
                     cur.execute(statement)
```
                     
The problem of replacing string formating by ? is not important here:

> Usually your SQL operations will need to use values from Python variables. You shouldn’t assemble your query using Python’s string operations because doing so is insecure; it makes your program vulnerable to an SQL injection attack ([1.13. sqlite3][3] )


  [1]: http://www.sqlite.org/lang_createindex.html
  [2]: http://mkaz.com/solog/python-string-format
  [3]: http://docs.python.org/2/library/sqlite3.html
