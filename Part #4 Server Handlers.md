# Part #4: Server Handlers

There are 4 types of Class for writing code in B4J server.
* Standard Class
* Server Filter
* Server Handler
* Server WebSocket

In this tutorial, we will focus on Server Handler.

This is the class that we use most of the time to write all of the business logic for the API.

## Conventions
1. For this Web API Server template, we use the following naming convention to differentiate the purpose of the handler class.
   * Api Handler - to read the Request from the URI contains the path /api and output the Response as JSON
   * Web Handler - to read the Request from the URI without the /api path and output the Response as HTML
2. For e.g there are 2 handlers for Categories i.e CategoriesApiHandler and CategoriesWebHandler.
   * Spoiler
4. You can either have Api handler, Web handler or both.
5. As you can guess, CategoriesApiHandler is use for taking care of the API endpoints for Categories while CategoriesWebHandler is use to display the web page to manage the Categories.
6. IndexWebHandler is use to display the index page when a user browse to the root path.
7. The index page calls the API endpoints from ProductsApiHandler and FindApiHandler.
8. HelpHandler is use to display the API documentation page. It will be use to call any API endpoint that you have created.
9. Each Web handler is linked to its respective JavaScript file to make AJAX calls and process the returned JSON Response from an Api handler.

## Adding Server Handler
1. There are 2 ways to add a Server Handler.
2. We can click on the menu Project, Add New Module, Class Module.
   * Spoiler: Click on the menu Project
3. However, I recommend another way. Right click on the Handlers group in Modules tab and select Server Handler. \
   By this way, we don't need to drag the class into the group later.
   * Spoiler: Right click on Modules group
4. Enter the name of the handler and click Ok.
   * Spoiler: Enter the name of the handler
6. An empty class with a Handle sub is generated.
```B4X
'Handler class
Sub Class_Globals
  
End Sub

Public Sub Initialize
  
End Sub

Sub Handle(req As ServletRequest, resp As ServletResponse)
  
End Sub
```

Next tutorial: [Part #5: Using Code Snippets](https://github.com/pyhoon/web-api-server-tutorial/blob/main/Part%20%235%20Using%20Code%20Snippets.md)
