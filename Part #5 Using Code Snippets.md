Part #5: Using Code Snippets
We have learned in Part #4 that we need to write our code inside Server Handlers.
We can save time by generating some boilerplate code using Code Snippets included inside WebApiUtils.
We continue to work with UsersApiHandler.
Let's delete all the code but left the Class_Globals sub.
Spoiler: Delete all the initial code
Note: We can also use a Standard class.
Put your cursor inside Class_Globals sub, now start typing "globals".
Spoiler: Activate Code Snippets
Select the "Api Class Globals" Code Snippet using the Up/Down key and press Enter.
The result is the following:
B4X:
Sub Class_Globals
        Private Request As ServletRequest
        Private Response As ServletResponse
        Private HRM As HttpResponseMessage
        Private DB As MiniORM
        Private Method As String
        Private Elements() As String
        Private ElementId As Int
End Sub
The variable names are quite self explanatory. We will be using HttpResponseMessage from WebApiUtils and MiniORM class.
Put the cursor after the Class_Globals sub and start typing "handler".
Use the Up/Down key to select "Api Handler" code snippet then press Enter.
Spoiler: Api Handler code snippet
The default code is added. You will notice there are some texts are highlighted.
Spoiler: Api handler boilerplate code
Replace "EndPoints" with "Users" for plural word and "EndPoint" with "User" for singular word.
Spoiler: Replace the highlighted text
Press Tab key to switch to next occurrences if they exist.
Here, we also need to change the "TableName" to the name of your users table.
Spoiler: TableName
When the cursor returned to the first highlighted text, we can press Enter to accept the changes.
Now the handler is ready. You can make further customization to this handler.
Take note that we still need to create the users table and add the handler to the server.
Main:
Sub AppStart (Args() As String)    srvr.Initialize("")
    srvr.AddHandler("", "IndexWebHandler", False)                           ' Home handler
    srvr.AddHandler("/categories/*", "CategoriesWebHandler", False)         ' Web handler
    srvr.AddHandler("/api/categories/*", "CategoriesApiHandler", False)     ' API handler
    srvr.AddHandler("/api/products/*", "ProductsApiHandler", False)         ' API handler
    srvr.AddHandler("/api/users/*", "UsersApiHandler", False)               ' API handler
    srvr.AddHandler("/api/find/*", "FindApiHandler", False)                 ' API handler
    srvr.AddHandler("/help", "HelpHandler", False)                          ' Help handler
To add the endpoints to API documentation, add the code inside ReadHandlers sub.
HelpHandler:
Public Sub ReadHandlers (FileDir As String) As String
    Dim strHtml As String
    Log(TAB)
    Log("Generating Help page ...")
    Dim verbs(4) As String = Array As String("GET", "POST", "PUT", "DELETE")
    Dim DocumentedHandlers As List
    DocumentedHandlers.Initialize
    Dim Handlers As List
    Handlers.Initialize
    Handlers.Add("CategoriesApiHandler")
    Handlers.Add("ProductsApiHandler")
    Handlers.Add("UsersApiHandler")
    Handlers.Add("FindApiHandler")
Note: You can apply the similar steps for creating Web handler using code snippets.

This is the end of tutorial for Part #5
