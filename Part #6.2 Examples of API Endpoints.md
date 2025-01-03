Part #6.2: Examples of API Endpoints

GetUsers
```B4X
Private Sub GetUsers
    ' #Desc = Read all Users
    DB.Table = "tbl_users"
    DB.Query
    HRM.ResponseCode = 200
    HRM.ResponseData = DB.Results
    ReturnApiResponse
    DB.Close
End Sub
```
This sub is called when the request URL matched an empty pattern with GET method.
It is use to get all the rows in users table.
Important: In order for HelpHandler to read this handler to generate the API Documentation, the name of the sub must be started with "Get".
The first line is a comment. We use #Desc to tell HelpHandler class that the text after the equals sign is the description of the API that will appear in the documentation.
Spoiler: Setting description for API end point
Similar to how we create a table, we need to pass the table name using the Table property when we want to query rows from a table.
```B4X
DB.Table = "tbl_users"
```
Next, we just need to call Query method.
```B4X
DB.Query
```
We want to return a status code 200 to the response.
```B4X
HRM.ResponseCode = 200
```
Since we are expecting the rows are returned as a JSON array or list, we pass the query result DB.Results (which is a list) to HRM.ResponseData.
```B4X
HRM.ResponseData = DB.Results
```
Then we call ReturnApiResponse to write the JSON output to the server response.
Finally we close the MiniORM object which will close the SQL resultset object especially for MySQL database.
```B4X
DB.Close
```

GetUserById
```B4X
Private Sub GetUserById (Id As Int)
    ' #Desc = Read one User by id
    ' #Elements = [":id"]
    DB.Table = "tbl_users"
    DB.Find(Id)
    If DB.Found Then
        HRM.ResponseCode = 200
        HRM.ResponseObject = DB.First
    Else
        HRM.ResponseCode = 404
        HRM.ResponseError = "User not found"
    End If
    ReturnApiResponse
    DB.Close
End Sub
```
This sub is called when the request URL matched an id pattern with GET method. \
It is use to get a single row in users table if the id is existed. \
Adding #Elements comment and parameter with type (e.g Id As Int) for the sub dictates how the documentation will appear. \
Spoiler: How code affects Api documentation
Similar to the GetUsers sub, we pass the table name to MiniORM.
```B4X
DB.Table = "tbl_users"
```
To query for a single row from users table by id, we can use the Find method.
```B4X
DB.Find(Id)
```
If a row is found then we return the row as a single object.
```B4X
If DB.Found Then
    HRM.ResponseCode = 200
    HRM.ResponseObject = DB.First
```
We use the First method to ensure that only one object is read. \
WebApiUtils through ReturnHttpResponse method will determine which format to return as JSON output based on the SimpleResponse settings. \
It means the return value can be an array or an object. \
If the id is not existed, we want to return a message to the API. \
We assign status code 404 to indicate the item is not found. \
```B4X
Else
    HRM.ResponseCode = 404
    HRM.ResponseError = "User not found"
```
Finally, we write the JSON output to server response and close the MiniORM object.
```B4X
ReturnApiResponse
DB.Close
```
PostUser
```B4X
Private Sub PostUser
    ' #Desc = Add a new User
    ' #Body = {<br>&nbsp;"name": "User_name"<br>}
    Dim data As Map = WebApiUtils.RequestData(Request)
    If Not(data.IsInitialized) Then
        HRM.ResponseCode = 400
        HRM.ResponseError = "Invalid json object"
        ReturnApiResponse
        Return
    End If

    ' Check whether required keys are provided
    Dim RequiredKeys As List = Array As String("key1", "key2", "key3")
    For Each requiredkey As String In RequiredKeys
        If data.ContainsKey(requiredkey) = False Then
            HRM.ResponseCode = 400
            HRM.ResponseError = $"Key '${requiredkey}' not found"$
            ReturnApiResponse
            Return
        End If
    Next
   
    ' Check conflict User name
    DB.Table = "tbl_users"
    DB.Where = Array("User_name = ?")
    DB.Parameters = Array As String(data.Get("user_name"))
    DB.Query
    If DB.Found Then
        HRM.ResponseCode = 409
        HRM.ResponseError = "User already exist"
        ReturnApiResponse
        DB.Close
        Return
    End If
   
    ' Insert new row
    DB.Reset
    DB.Columns = Array("User_name", "created_date")
    DB.Parameters = Array(data.Get("User_name"), data.GetDefault("created_date", WebApiUtils.CurrentDateTime))
    DB.Save
   
    ' Retrieve new row
    HRM.ResponseCode = 201
    HRM.ResponseObject = DB.First
    HRM.ResponseMessage = "User created successfully"
    ReturnApiResponse
    DB.Close
End Sub
```
This sub is called when the request URL matched the empty pattern with POST method. \
It is use to create a new resource in this case the users table. \
Meaning it will execute an INSERT query to the database using MiniORM. \
Notice that we use a comment ' #Body to display the sample body. (Similar for PUT end point) \
Spoiler: Construct body hint in API documentation \
Take note that we only can write the comment in a single line. \
For POST request, we need to read the data submitted in the POST body. \
```B4X
Dim data As Map = WebApiUtils.RequestData(Request)
```
If the submitted payload is a valid JSON string, it can be parse using RequestData method of WebApiUtils and assign the result into a Map.
```B4X
If Not(data.IsInitialized) Then
    HRM.ResponseCode = 400
    HRM.ResponseError = "Invalid json object"
    ReturnApiResponse
    Return
End If
```
If the string is invalid, we return a result with status code 400 and exit the process.
We also want to check if all the required keys are provided.
```B4X
' Check whether required keys are provided
Dim RequiredKeys As List = Array As String("key1", "key2", "key3")
For Each requiredkey As String In RequiredKeys
    If data.ContainsKey(requiredkey) = False Then
        HRM.ResponseCode = 400
        HRM.ResponseError = $"Key '${requiredkey}' not found"$
        ReturnApiResponse
        Return
    End If
Next
```
For e.g we can replace key1 with user_name, key2 with email and key3 with password.
Then we check for existing records to avoid duplicate.
```B4X
' Check conflict User name
DB.Table = "tbl_users"
DB.Where = Array("User_name = ?")
DB.Parameters = Array As String(data.Get("User_name"))
DB.Query
If DB.Found Then
    HRM.ResponseCode = 409
    HRM.ResponseError = "User already exist"
    ReturnApiResponse
    DB.Close
    Return
End If
```
In the case of users, it is more useful if we check for unique emails. We can replace the above code to check for emails.
```B4X
' Check conflict emails
DB.Table = "tbl_users"
'DB.Where = Array("user_email = ?")
'DB.Parameters = Array As String(data.Get("user_email"))
DB.WhereValue(Array("user_email = ?"), Array(data.Get("user_email")))
DB.Query
```
There is a new shortcut method WhereValue in MiniORMUtils to replace method Where and Parameters. \
Next, we can insert the data. \
Notice we use method Reset to ensure any conditions or parameters are cleared before we reuse the MiniORM object. 
```B4X
' Insert new row
DB.Reset
```
We specify the column names in order inside an array and assign to Columns property of MiniORM.
```B4X
DB.Columns = Array("user_name", "user_email", "user_password", "created_date")
```
Next, we specify the values associated with the columns into Parameters property as an array of objects.
```B4X
DB.Parameters = Array(data.Get("user_name"), data.Get("user_email"), data.Get("user_password"), data.GetDefault("created_date", WebApiUtils.CurrentDateTime))
```
Then we call the method Save. This will create an INSERT command and MiniORM will execute it.
```B4X
DB.Save
```
If the command executed successfully, we return a result with status code 201 to indicate a new resource is created. \
We pass the first object from MiniORM which is queried based on the newly created id. \
We can return the object by assigning it to ResponseObject. \
We can also assign a message using the ResponseMessage property. \
Finally, we write the JSON output to server response and close the MiniORM object.
```B4X
ReturnApiResponse
DB.Close
```
Take note of the key names inside the data map. For e.g use the lowercase data.Get("user_name") instead of data.Get("User_name").
