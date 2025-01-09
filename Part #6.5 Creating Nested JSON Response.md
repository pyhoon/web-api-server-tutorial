# Part #6.5 Creating Nested JSON Response

1. Sometimes we want to return a more complex JSON response.
2. Let say we have 2 categories and each category has its own products associated with it by category_id.
3. We want to return a JSON like the following:
```JSON
[
  {
    "category_name": "Hardware",
    "id": 1,
    "products": [
      {
        "id": 2,
        "product_name": "Hammer"
      }
    ]
  },
  {
    "category_name": "Toys",
    "id": 2,
    "products": [
      {
        "id": 1,
        "product_name": "Teddy Bear"
      },
      {
        "id": 3,
        "product_name": "Optimus Prime"
      }
    ]
  }
]
```
4. First we need to add a new endpoint e.g http://127.0.0.1:8080/api/categories/products.
5. In CategoriesApiHandler class, add a new variable ElementKey in Class_Globals to support string element.
```B4X
Sub Class_Globals
    ...
    Private ElementId As Int        ' numeric key
    Private ElementKey As String    ' string key
End Sub
```
6. In Handle sub, add a new check for ElementMatch.
```B4X
Sub Handle (req As ServletRequest, resp As ServletResponse)
    ...
    Select Method
        Case "GET"
            ...
            If ElementMatch("id") Then
                GetCategoryById(ElementId)
                Return
            End If
            If ElementMatch("key") Then
                If ElementKey = "products" Then
                    GetCategoriesAndProducts
                    Return
                End If
            End If
        ...
    End Select
    ReturnBadRequest
End Sub
```
7. We also need to update the code for ElementMatch. Add the highlighted code.
```B4X
Private Sub ElementMatch (Pattern As String) As Boolean
    Select Pattern
        Case ""
           ...
        Case "id"
           ...
        Case "key"
            If Elements.Length = 1 Then
                If IsNumber(Elements(0)) = False Then
                    ElementKey = Elements(0)
                    Return True
                End If
            End If
    End Select
    Return False
End Sub
```
8. Now add a new sub call GetCategoriesAndProducts.
```B4X
Private Sub GetCategoriesAndProducts
    ' #Desc = Get all Categories with their Products
    ' #Elements = ["products"]
    DB.Table = "tbl_categories"
    DB.Select = Array("id", "category_name")
    DB.Query
    Dim Categories As List = DB.Results
    For Each category As Map In Categories
        DB.Table = "tbl_products"
        DB.Select = Array("id", "product_name")
        DB.WhereValue(Array("category_id = ?"), Array(category.Get("id")))
        DB.Query
        Dim products As List = DB.Results
        category.Put("products", products) ' add new key
    Next
    HRM.ResponseCode = 200
    HRM.ResponseData = Categories
    ReturnApiResponse
    DB.Close
End Sub
```
9. You can see we first query table "tbl_categories" for all categories. To simplify view, we only select column id and category_name.
10. We pass the DB.Results into Categories list.
11. Then we loop the list to get the item as category map.
12. We will need to get the id of each category and use it to query the products from "tbl_products" having the same category id.
13. Again to simplify view of this tutorial, we only select column id and product_name.
14. We pass the DB.Results into products list.
15. Then we add a new key "products" with the products list as its value to put back to the category map.
16. The list we are interested is Categories to pass to HRM.ResponseData
17. Finally call ReturnApiResponse and close the MiniORM object as usual.

Test API

That's it.
