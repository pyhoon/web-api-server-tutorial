Part #6.1: Declaring MiniORM in Server Handler

Referring to the Part #5: Using Code Snippets tutorial.

On top of the code, we see there are some variables declared as Private.
```B4X
Sub Class_Globals
        Private Request As ServletRequest
        Private Response As ServletResponse
        Private HRM As HttpResponseMessage
        Private DB As MiniORM
        Private Method As String
        Private Elements() As String
        Private ElementId As Int
End Sub
```
Request and Response are passing from Handle sub so we can use them through out the entire handler class.
```B4X
Sub Handle (req As ServletRequest, resp As ServletResponse)
    Request = req
    Response = resp
    ...
End Sub
```
We declared a variable HRM as HttpResponseMessage for generating the JSON output or response.
```B4X
Private HRM As HttpResponseMessage
```
HttpResponseMessage is a custom type defined in WebApiUtils library.

We declared DB as MiniORM for working with our database.
```B4X
Private DB As MiniORM
```
MiniORM is a class from MiniORMUtils library.
We declared a String array Elements() for storing the elements from the request URL.
```B4X
Private Elements() As String
```
Last variable is ElementId for storing the Id element.
```B4X
Private ElementId As Int
```
Initialization
We use the Initialize sub to initialize 2 variables, HRM and DB from the public variables we declared in Main module.
```B4X
Public Sub Initialize
    HRM.Initialize
    HRM.SimpleResponse = Main.Config.SimpleResponse
    DB.Initialize(Main.DBOpen, Main.DBEngine)
End Sub
```
Handle sub
The Handle sub look like following:
```B4X
Sub Handle (req As ServletRequest, resp As ServletResponse)
    Request = req
    Response = resp
    Method = Request.Method.ToUpperCase
    Dim FullElements() As String = WebApiUtils.GetUriElements(Request.RequestURI)
    Elements = WebApiUtils.CropElements(FullElements, 3) ' 3 For Api handler
    Select Method
        Case "GET"
            ...
        Case "POST"
            ...
        Case "PUT"
            ...
        Case "DELETE"
            ...
        Case Else
            Log("Unsupported method: " & Method)
            ReturnMethodNotAllow
            Return
    End Select
    ReturnBadRequest
End Sub
```
The Method variable read from the Request variable will be use for further routing.

We can use GetUriElements method of WebApiUtils to fill the FullElements array.
```B4X
Dim FullElements() As String = WebApiUtils.GetUriElements(Request.RequestURI)
```
Instead of counting the elements from 0 for the root element from the URL, what we are interested are the elements after the /api (for Api Handler).

Therefore we use CropElements method to "slice" or crop out the first elements.
```B4X
Elements = WebApiUtils.CropElements(FullElements, 3) ' 3 For Api handler
```
From this point, we can think that the first Element e.g Id is accessed using the index 0.
```B4X
ElementId = Elements(0)
```
You can see the logic inside ElementMatch sub.

Next, we will use Select-Case to control the flow of the request falls to the right sub.

Control Flow

Base on the Select-Case of variable Method, we can route to 2 API end points in this example.

The first end point is without Id where we pass an empty string pattern to the ElementMatch sub.

e.g URL: http://127.0.0.1:8080/api/users
```B4X
Select Method
    Case "GET"
        If ElementMatch("") Then
            GetUsers
            Return
        End If
        ...
    ...
End Select
```
If ElementMatch returns True then we can proceed to call GetUsers sub to return all the rows inside users table.

The second end point is with an Id where we pass the "id" pattern to the ElementMatch sub.
e.g URL: http://127.0.0.1:8080/api/users/2
```B4X
If ElementMatch("id") Then
    GetUserById(ElementId)
    Return
End If
```
Important: For every match, insert Return keyword before the End If keywords like the examples above. \
Missing this keyword will cause the response Api returns the Bad Request output too. In other word, a malformed JSON will be returned. \
The same steps apply to other Methods such as POST, PUT and DELETE depending on the Endpoint pattern you want to create. \
We also check if there are request by other Method (e.g PATCH) and we don't want to allow such request to proceed.
```B4X
Case Else
    Log("Unsupported method: " & Method)
    ReturnMethodNotAllow
    Return
```
This is also the case where you want to create an Api that only allow POST then the remaining request can be consider under the Case Else flow. \
Any route that does not match the desired pattern will fall under Bad Request and handled by ReturnBadRequest sub.

**Commonly Use Subs**

We can shorten some of the long codes by using extra subs. \
This is an example:
```B4X
Private Sub ReturnApiResponse
    WebApiUtils.ReturnHttpResponse(HRM, Response)
End Sub
```
So instead of writing the full code, we can just use one word in our code.

You can see examples of using ReturnBadRequest, ReturnMethodNotAllow and ReturnErrorUnprocessableEntity.
