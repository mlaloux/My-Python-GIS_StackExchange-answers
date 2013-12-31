From [Save input in plugin as pdf format file](http://gis.stackexchange.com/questions/81621/save-input-in-plugin-as-pdf-format-file/)


You need a Python script to to convert your resulting text file in PDF or a Python module to write pdf files and there are many... (look at [PiPY:pdf][1]).

####1) first solution with a Python script:

 - [Text to PDF Converter (rewrite) (Python recipe) ][10], for example.

####2) second solution with a Python module

The problem is that you need to install the module in the Python used by QGIS (easy in Mac OS X or Linux, difficult in Windows) or in the folder of your plugin.

 - the most complete module is [reportlab][7], but it is difficult to use (look at [reportlab-userguide.pdf][8])

 - One of the easiest is the pure Python [PyFPDF][2] module (it is used by Joel Lawhead in his book, [Learning Geospatial Analysis with Python][3], to produce PDF reports with maps)

```python


    def _save(self, simpan):  
        import fpdf
        # Portrait, millimeter units, A4 page size     
        pdf=fpdf.FPDF("P", "mm", "A4")
        # Set font: Times, normal, size 10
        pdf.set_font('Times','', 10)
        # Layout cell: 0 x 5 mm, text, no border, Left
        pdf.cell(0,5,'Input 1 : ' + self.ui.lineInput1.text(), border=0, align="L" )
        pdf.cell(0,5,'Input 2 : ' + self.ui.lineInput2.text(), border=0, align="L")
        pdf.cell(0,5,'Recomendation : ' + (str(compare), border=0, align="L")
        pdf.cell(0,5,'Data 1 :' +  self.ui.lineCond1.text(), border=0, align="L" )
        pdf.cell(0,5,'Data 2 :' + self.ui.lineCond2.text(), border=0, align="L" )
       	pdf.output( simpan+'.pdf','F')
       	
```

 - Another solution is to use the single file [PDFWriter.py][4] from the [xtopdf][5] toolkit (as easy as [PyFPDF][6], but it requires the [reportlab][7] module) of  [Vasudev Ram][9] but I do not know if you can use a single file and you must install [reportlab][7]... 

```python

    def _save(self, simpan):  
        from PDFWriter import PDFWriter
        pw = PDFWriter(simpan+'.pdf')
        pw.setFont("Courier", 10)
        pw.writeLine('Input 1 : ' + self.ui.lineInput1.text() )
        pw.writeLine('Input 2 : ' +  self.ui.lineInput2.text() )
        pw.writeLine('Recomendation :' + (str(compare))
        pw.writeLine('Data 1 : ' +  self.ui.lineCond1.text() )
        pw.writeLine('Data 2 : ' + self.ui.lineCond2.text() )
       	pw.close

```


  [1]: https://pypi.python.org/pypi?:action=search&term=Pdf&submit=search
  [2]: http://code.google.com/p/pyfpdf/
  [3]: http://www.packtpub.com/learning-geospatial-analysis-with-python/book
  [4]: https://bitbucket.org/vasudevram/xtopdf/src/854a19c557af9040d74e23c10fa2a6d108d77c68/PDFWriter.py?at=default
  [5]: https://bitbucket.org/vasudevram/xtopdf
  [6]: http://code.google.com/p/pyfpdf/
  [7]: https://bitbucket.org/rptlab/reportlab
  [8]: https://www.reportlab.com/docs/reportlab-userguide.pdf
  [9]: http://jugad2.blogspot.be/
  [10]: http://code.activestate.com/recipes/532908/
