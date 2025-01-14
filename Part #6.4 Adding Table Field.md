# Part #6.4: Adding Table Field
1. This is a guide to add an Integer field for "opening stock" in table tbl_products.
2. In this example, we make it compulsory to supply the value during Add new product in index page.

## Modify Product Table
1. Go to CreateDatabase sub in Main module.
2. We need to add the following code
```B4X
MDB.Columns.Add(MDB.CreateORMColumn2(CreateMap("Name": "opening_stock", "Type": MDB.INTEGER, "Default": "0")))
```
3. Here we are adding a column of type integer and default the value to 0.
4. Now the code will look like the following:
```B4X
MDB.Table = "tbl_products"
MDB.Columns.Add(MDB.CreateORMColumn2(CreateMap("Name": "category_id", "Type": MDB.INTEGER)))
MDB.Columns.Add(MDB.CreateORMColumn2(CreateMap("Name": "product_code", "Length": "12")))
MDB.Columns.Add(MDB.CreateORMColumn2(CreateMap("Name": "product_name")))
MDB.Columns.Add(MDB.CreateORMColumn2(CreateMap("Name": "product_price", "Type": MDB.DECIMAL, "Length": "10,2", "Default": "0.00")))
MDB.Columns.Add(MDB.CreateORMColumn2(CreateMap("Name": "opening_stock", "Type": MDB.INTEGER, "Default": "0")))
MDB.Foreign("category_id", "id", "tbl_categories", "", "")
MDB.Create
```
5. Next, we open ProductsApiHandler class to modify the code for POST and PUT endpoints.
6. In ```PostProduct``` sub we update the #Body comment to accept a new item with key "stock".
```B4X
' #Body = {<br>&nbsp;"cat_id": category_id,<br>&nbsp;"code": "product_code",<br>&nbsp;"name": "product_name",<br>&nbsp;"price": product_price,<br>&nbsp;"stock": 0<br>}
```
7. This item will be map to our column "opening_stock". \
   ![Spoiler](#)
8. We add a code to check the "stock" key in request body or json payload and replace it with the correct table column.
```B4X
If data.ContainsKey("stock") Then
    data.Put("opening_stock", data.Get("stock"))
    data.Remove("stock")
End If
```

![Spoiler](#)

9. Add the column as required keys.
```B4X
' Check whether required keys are provided
Dim RequiredKeys As List = Array As String("category_id", "product_code", "product_name", "opening_stock") ' "product_price" is optional
For Each requiredkey As String In RequiredKeys
    If data.ContainsKey(requiredkey) = False Then
        HRM.ResponseCode = 400
        HRM.ResponseError = $"Key '${requiredkey}' not found"$
        ReturnApiResponse
        Return
    End If
Next
```

![Spoiler](#)

10. Add new column to the Columns array of MiniORM.
```B4X
' Insert new row
DB.Reset
DB.Columns = Array("category_id", _
"product_code", _
"product_name", _
"product_price", _
"opening_stock", _
"created_date")
DB.Parameters = Array(data.Get("category_id"), _
data.Get("product_code"), _
data.Get("product_name"), _
data.GetDefault("product_price", 0), _
data.GetDefault("opening_stock", 0), _
data.GetDefault("created_date", WebApiUtils.CurrentDateTime))
DB.Save
```
11. Similarly in ```PutProductById``` sub. The completed code should look like the following.
```B4X
Private Sub PutProductById (Id As Int)
    ' #Desc = Update Product by id
    ' #Body = {<br>&nbsp;"cat_id": category_id,<br>&nbsp;"code": "product_code",<br>&nbsp;"name": "product_name",<br>&nbsp;"price": product_price,<br>&nbsp;"stock": 0<br>}
    ' #Elements = [":id"]
    Dim data As Map = WebApiUtils.RequestData(Request)
    If Not(data.IsInitialized) Then
        HRM.ResponseCode = 400
        HRM.ResponseError = "Invalid json object"
        ReturnApiResponse
        Return
    End If

    ' Deprecated: Make it compatible with Web API Client v1 (will be removed)
    If data.ContainsKey("cat_id") Then
        data.Put("category_id", data.Get("cat_id"))
        data.Remove("cat_id")
    End If
    If data.ContainsKey("code") Then
        data.Put("product_code", data.Get("code"))
        data.Remove("code")
    End If
    If data.ContainsKey("name") Then
        data.Put("product_name", data.Get("name"))
        data.Remove("name")
    End If
    If data.ContainsKey("price") Then
        data.Put("product_price", data.Get("price"))
        data.Remove("price")
    End If
    If data.ContainsKey("stock") Then
        data.Put("opening_stock", data.Get("stock"))
        data.Remove("stock")
    End If

    ' Check conflict product code
    DB.Table = "tbl_products"
    DB.Where = Array("product_code = ?", "id <> ?")
    DB.Parameters = Array As String(data.Get("product_code"), Id)
    DB.Query
    If DB.Found Then
        HRM.ResponseCode = 409
        HRM.ResponseError = "Product Code already exist"
        ReturnApiResponse
        DB.Close
        Return
    End If

    DB.Find(Id)
    If DB.Found = False Then
        HRM.ResponseCode = 404
        HRM.ResponseError = "Product not found"
        ReturnApiResponse
        DB.Close
        Return
    End If

    DB.Reset
    DB.Columns = Array("category_id", _
    "product_code", _
    "product_name", _
    "product_price", _
    "opening_stock", _
    "modified_date")
    DB.Parameters = Array(data.Get("category_id"), _
    data.Get("product_code"), _
    data.Get("product_name"), _
    data.GetDefault("product_price", 0), _
    data.GetDefault("opening_stock", 0), _
    data.GetDefault("modified_date", WebApiUtils.CurrentDateTime))
    DB.Id = Id
    DB.Save

    HRM.ResponseCode = 200
    HRM.ResponseMessage = "Product updated successfully"
    HRM.ResponseObject = DB.First
    ReturnApiResponse
    DB.Close
End Sub
```
12. Backend API is done. Now we go to front end.

## Modify the Front End
1. Open the Files folder of the project with a text editor.
2. Open the index.html file.
3. We will focus on the modal form for "new" and "edit".
   
![Spoiler](#)

4. We can copy the code for price and replace the word "price" to "stock".

![Spoiler](#)

5. We also copy this code for "edit" but we need to change the id to "stock1" because the id must be unique.

![Spoiler](#)

6. Now we move on to the JavaScript part.
7. Because this template supports 3 types of JSON output as set in SimpleResponse, 3 different JS files are used.
8. If you are using the default, the server will load the search.simple.js file when loading index.html page.

![Spoiler](#)

9. The code for jQuery Validate is currently checking for product code and product name which are set as compulsory.
10. Category id is not checked because it is from drop down list, user must select an item.
11. Product Price also not checked because we let it as not compulsory. If user did not enter anything to input box then the value is default to 0.
12. Now we will add a code to validate user input to enter "opening stock".

![Spoiler](#)

13. This step is optional if you don't want to set it as compulsory or required field.
14. We will leave the #update part as not compulsory for "opening stock" field.
15. Next, we write the code for displaying the search result table.
16. Find the code for #btnsearch click.

![Spoiler](#)

17. We need to add a new column "Stock" after "Price" to the html table header. \
    JavaScript:
```JavaScript
tbl_head = "<thead class=\"bg-light\"><th style=\"text-align: right; width: 60px\">#</th><th>Code</th><th>Category</th><th>Name</th><th style=\"text-align: right\">Price</th><th style=\"text-align: right\">Stock</th><th style=\"width: 90px\">Actions</th></thead>"
```
18. Then we add new variables for the column and value.
    
![Spoiler](#)

19. Add the following code to assign the value to table cell and temporary variable stock.
    
![Spoiler](#)

20. Add the temporary variable for stock to col_edit so it can pass the data to the modal form when edit button is clicked. \
JavaScript:
```JavaScript
col_edit = "<td><a href=\"#edit\" class=\"text-primary mx-2\" data-toggle=\"modal\"><i class=\"edit fa fa-pen\" data-toggle=\"tooltip\" data-id=\"" + id + "\" data-code=\"" + code + "\" data-category=\"" + catid + "\" data-name=\"" + name + "\" data-price=\"" + price + "\" data-stock=\"" + stock + "\" title=\"Edit\"></i></a> <a href=\"#delete\" class=\"text-danger mx-2\" data-toggle=\"modal\"><i class=\"delete fa fa-trash\" data-toggle=\"tooltip\" data-id=\"" + id + "\" data-code=\"" + code + "\" data-category=\"" + catid + "\" data-name=\"" + name + "\" title=\"Delete\"></i></a></td>"
```

![Spoiler](#)

21. Then add col_stock to tbl_body. \
JavaScript:
```JavaScript
tbl_body += "<tr>" + col_id + col_code + col_category + col_name + col_price + col_stock + col_edit + "</tr>"
```

![Spoiler](#)

22. We also need to update the click event for the inline edit button.
23. This is to load the value from result table into the edit modal form.

![Spoiler](#)

24. The search button code is done but there is a function to load the table when the page loads without clicking on button search.
25. There is a function using GET request to get all the rows by default using $.getJSON("/api/find") ajax call.
    
![Spoiler](#)

26. This part will be almost the same for the #btnSearch.
27. The updated script will look like the following. \
JavaScript:
```JavaScript
  $.getJSON("/api/find", function (response) {
    var tbl_head = ""
    var tbl_body = ""
    if (response.length) {
      tbl_head = "<thead class=\"bg-light\"><th style=\"text-align: right; width: 60px\">#</th><th>Code</th><th>Category</th><th>Name</th><th style=\"text-align: right\">Price</th><th style=\"text-align: right\">Stock</th><th style=\"width: 90px\">Actions</th></thead>"
      tbl_body += "<tbody>"
      $.each(response, function () {
        var col_id = ""
        var col_code = ""
        var col_category = ""
        var col_name = ""
        var col_price = ""
        var col_stock = ""
        var col_edit = ""
        var id
        var code
        var name
        var price
        var stock
        var catid
        $.each(this, function (key, value) {
          if (key == "id") {
            col_id = "<td class=\"align-middle\" style=\"text-align: right\">" + value + "</td>"
            id = value
          }
          else if (key == "product_code") {
            col_code = "<td class=\"align-middle\">" + value + "</td>"
            code = value
          }
          else if (key == "category_name") {
            col_category = "<td class=\"align-middle\">" + value + "</td>"
          }
          else if (key == "product_name") {
            col_name = "<td class=\"align-middle\">" + value + "</td>"
            name = value
          }
          else if (key == "product_price") {
            col_price = "<td class=\"align-middle\" style=\"text-align: right\">" + value + "</td>"
            price = value
          }
          else if (key == "opening_stock") {
            col_stock = "<td class=\"align-middle\" style=\"text-align: right\">" + value + "</td>"
            stock = value
          }         
          else if (key == "category_id") {
            catid = value
          }
        })
        col_edit = "<td><a href=\"#edit\" class=\"text-primary mx-2\" data-toggle=\"modal\"><i class=\"edit fa fa-pen\" data-toggle=\"tooltip\" data-id=\"" + id + "\" data-code=\"" + code + "\" data-category=\"" + catid + "\" data-name=\"" + name + "\" data-price=\"" + price + "\" data-stock=\"" + stock + "\" title=\"Edit\"></i></a> <a href=\"#delete\" class=\"text-danger mx-2\" data-toggle=\"modal\"><i class=\"delete fa fa-trash\" data-toggle=\"tooltip\" data-id=\"" + id + "\" data-code=\"" + code + "\" data-category=\"" + catid + "\" data-name=\"" + name + "\" title=\"Delete\"></i></a></td>"
        tbl_body += "<tr>" + col_id + col_code + col_category + col_name + col_price + col_stock + col_edit + "</tr>"
      })
      tbl_body += "</tbody>"
    }
    else {
      tbl_body = "<tr><td>No results</td></tr>"
    }
    $("#results table").html(tbl_head + tbl_body)
  })
})
```
28. Delete the sqlite database file and run the project again in debug.

![Spoiler](#)

Next tutorial: [Part #6.5 Creating Nested JSON Response](https://github.com/pyhoon/web-api-server-tutorial/blob/main/Part%20%236.5%20Creating%20Nested%20JSON%20Response.md)
