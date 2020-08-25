# VB.NET---Create-Barcode-Using-Crystal-Report
Hi friends today i explain about how to create 'Barcode' and 'Print' 
in vb.net using crystal report


# Needs
1. Crstal Reports for SAP Business One - 406 MB File Size or More
2. Visual studio	  - 3 GB or More
3. CRforVS_13_0_19(Crystal Report For Visual studio 2013) - 290 MB File size or More -For crystal report viewer contorl in .NET
4. Zen.Barcode.Core.dll   - 11 MB or More

Okay, Lets start

When you give Barcode text it will convert to barcode
To give the No.of count to generate barcode repeat

# Steps
1. Create DataSet in VB.NET
2. Create & Link 'VB.NET Dataset of DataTable' in 'Crystal Report' Manually
3. Design Crystal Report
4. Create Barcode in Vb.NET using 'Zen.Barcode.Core.dll'
5. Create datatable dynamically and add the data into it
6. Load the Dynamic DataTable into crystal report DataTable and Preview
7. Print the report

Okay lets we see step by step

# 1. Lets Create DataSet in VB.NET
- Right click projet in solution explorer --> Add --> New Item --> Select 'Data' in tree view --> Select 'DataSet' in list
- Create DataTable in DataSet --> Right click DataTable --> Add --> Column
--> Here i used three DataColumn are S.No, Barcode, Barcode_Text
--> Then Assign datatype for your created DataColumn
--> Select column --> Right Click --> Properties --> Select Datatype
--> S.No (DataType - UInt32), Barcode (DataType - Byte()), Batcode_Text (DataType String)
-->save it

# 2. Create & Link 'VB.NET Dataset' in 'Crystal Report' Manually
1. Copy the data set path in vb.net
- In Solution Explorer Right Click DataSet --> Properties --> Copy the Full path
2. Open Crystal report
- Start --> crystal report --> open it. 
- File --> New --> Blank Report. 
- In Tree view-->'+' Select Create New Connection-->Double click Database File-->Dialog Open 
- Past the copied full path here --> Click Open
- Now the DataTable will shown --> Select & Move Right --> Click Ok
- View --> Field Explorer --> Click Database Fields --> Click DataTable -->Now Linked DataSet

# 3. Design Crystal Report
- Suppress Report & Page header and Report & page footer
- Use the Detail section to design barcode
- Here I moved the Barcode Picture field in detail - This is barcode
- Right click Barcode Field-->Format graphics-->Picture tab-->set width,height : 133.3,33.3
- Note This is the minimum size of barcode 133.3,33.3
- Then right Click Detail Fit section
- save crystal report in application startup path as 'barcode.rpt'
- save here

# 4. Create Barcode in Vb.NET using 'Zen.Barcode.Core.dll'
- Copy and paste the 'Zen.Barcode.Core.dll' in app startup path
- Add to reference and import it in your code
- Below code is used to convert barcode
```
Function FunCreateBarcode(ByVal barcoetext As String)
    Dim maxheight As Integer = 25
    Dim barcode128 As Code128BarcodeDraw = BarcodeDrawFactory.Code128WithChecksum
    Dim img As Image = barcode128.Draw(barcodetext, maxheight)
    Dim converter As ImageConverter = New ImageConverter()
    Dim barcodevalue As Byte() = CType(converter.ConvertTo(img, GetType(Byte())), Byte())
    Return barcodevalue
End Function
```
> In here I draw the barcode in image and convert it to bytes()

# 5.Create datatable dynamically and add the data into it
- Create Datatable same as DataSet
- Below Code is used to create Datatable dynamically.
```
Sub FunDynamicDataTable()
    dt = New DataTable
    With dt.Columns
       .Add("BARCODE", GetType(Byte()))
       .Add("BARCODETEXT", GetType(String))
       .Add("S.No", GetType(UInt32))
    End With
End Sub
```
- Then create Datarow to add one by one row record into the dynamic datatable by the given no.of copies

- Below code is used to insert datarow into dynamic databale
```
Sub FunLoadDataTable()
    FunDynamicDataTable()
    For i As Integer = 0 To Val(txtnoofbar.Text) - 1
        dtrow = dt.NewRow
        dtrow("BARCODE") = FunCreateBarcode(txtbartext.Text)
        dtrow("BARCODETEXT") = txtbartext.Text
        dtrow("S.No") = i + 1
        dt.Rows.Add(dtrow)
    Next
End Sub
```

# 6. Load the Dynamic DataTable into crystal report DataTable and Preview
- Add the 4 .dll file for crystal report are 
1. CrystalDecisions.CrystalReports.Engine.dll,
2. CrystalDecisions.ReportSource.dll,
3. CrystalDecisions.Shared.dll,
4. CrystalDecisions.Windows.Forms
- Imports the CrystalDecisions.CrystalReports.Engine
- Create Report document and load crystal report into the report document and load the datatable into it.
- Now Load the report document into the crystal report viewer
- Below code is used to load the dynamic data table into crystal report data table and show it in crystal report viewer
```
Sub FunPreview()
    If Val(txtnoofbar.Text) > 0 Then
        FunLoadDataTable()
        rpt = New ReportDocument
        rpt.Load(Application.StartupPath & "\Barcode.rpt")
        rpt.SetDataSource(dt)
        rptviewer.ReportSource = rpt
        rptviewer.Refresh()
    End If
End Sub
```

# 7. Print the report
- Create Report Document
- Load crytal report and datatable into it 
- set printing option and print it
- Below code is used to print the report
```
Sub FunPrint()
        rpt = New ReportDocument
        rpt.Load(Application.StartupPath & "\Barcode.rpt")
        rpt.SetDataSource(dt)
        rpt.PrintOptions.PaperOrientation = CrystalDecisions.Shared.PaperOrientation.Portrait
        rpt.PrintOptions.PaperSize = CrystalDecisions.Shared.PaperSize.DefaultPaperSize
        rpt.PrintToPrinter(1, False, 0, 0)
End Sub
```


Okay,
Lets Go...
Test the above code in vb.net

```
Imports CrystalDecisions.CrystalReports.Engine
Imports Zen.Barcode

Dim dt As DataTable = New DataTable
Dim dtrow As DataRow
Dim rpt As ReportDocument
Function FunCreateBarcode(ByVal barcodetext As String)
    Dim maxheight As Integer = 25
    Dim barcode128 As Code128BarcodeDraw = BarcodeDrawFactory.Code128WithChecksum
    Dim img As Image = barcode128.Draw(barcodetext, maxheight)
    Dim converter As ImageConverter = New ImageConverter()
    Dim barcodevalue As Byte() = CType(converter.ConvertTo(img, GetType(Byte())), Byte())
    Return barcodevalue
End Function
Sub FunLoadDataTable()
    FunDynamicDataTable()
    For i As Integer = 0 To Val(txtnoofbar.Text) - 1
        dtrow = dt.NewRow
        dtrow("BARCODE") = FunCreateBarcode(txtbartext.Text)
        dtrow("BARCODETEXT") = txtbartext.Text
        dtrow("S.No") = i + 1
        dt.Rows.Add(dtrow)
    Next
End Sub
Sub FunPreview()
    If Val(txtnoofbar.Text) > 0 Then
        FunLoadDataTable()
        rpt = New ReportDocument
        rpt.Load(Application.StartupPath & "\Barcode.rpt")
        rpt.SetDataSource(dt)
        rptviewer.ReportSource = rpt
        rptviewer.Refresh()
    End If
End Sub
Sub FunPrint()
    rpt = New ReportDocument
    rpt.Load(Application.StartupPath & "\Barcode.rpt")
    rpt.SetDataSource(dt)
    rpt.PrintOptions.PaperOrientation = CrystalDecisions.Shared.PaperOrientation.Portrait
    rpt.PrintOptions.PaperSize = CrystalDecisions.Shared.PaperSize.DefaultPaperSize
    rpt.PrintToPrinter(1, False, 0, 0)
End Sub
Sub FunDynamicDataTable()
    dt = New DataTable
    With dt.Columns
        .Add("BARCODE", GetType(Byte()))
        .Add("BARCODETEXT", GetType(String))
        .Add("S.No", GetType(UInt32))
    End With
End Sub
```
>
- If you have troble on running crystal report
- error like this:
"Could not load file or assembly 'file:///C:\Program Files\SAP BusinessObjects\Crystal Reports for 
.NET Framework 4.0\Common\SAP BusinessObjects Enterprise XI 4.0\win32_x86\dotnet1\crdb_adoplus.dll' 
or one of its dependencies. The system cannot find the file specified."

```
try to add this to your .config file (usually app.config)
<startup useLegacyV2RuntimeActivationPolicy="true">
<supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
</startup>
```
>
- Run it
- If you make mistake in column name the data not load correctly

Thanks for read this
please subscribe this channel for more https://www.youtube.com/channel/UCZFe63C5xv8sCMmDWPW3aqw?sub_confirmation=1

