# Part #1: Get Started

## Introduction
This tutorial is based on B4J project template [Web API Server v3.00](https://github.com/pyhoon/web-api-server-b4j).

## Installation
1. Download and save **Web API Server (3.00).b4xtemplate** file into B4J Additional Libraries folder.
2. You also need to download **WebApiUtils.b4xlib (v3.02)** and save inside the same folder.
3. It is recommended to use this template with **MiniORMUtils.b4xlib (v1.14)** which is a dependency by default.
4. You can put this library inside B4X Additional Libraries folder since it is a cross platform library.
   *Note: You can also use MinimaListUtils library or write your own SQL code using jSQL library.*
5. By default the template is based on SQLite database as backend.
6. Therefore you need sqlite-jdbc driver in your B4J or B4X Additional Libraries folder. B4J comes with sqlite-jdbc-3.7.2 as Internal Library.
7. If you want to use MySQL database, you need to use MySQL jdbc driver and put inside B4J Additional Libraries folder. e.g mysql-connector-java-5.1.49
   [Spoiler: B4J Additional Libraries]

## Create Project
1. Once all the required libraries and template are in place, you can start B4J IDE.
2. Select from File menu, New, Web API Server (3.x)
   [Spoiler: Select the project template]
3. Enter the name of your project as you like. Then click OK.
   [Spoiler: New Project]
4. Now the project is ready to run as it is.

## Running the Project
1. Click menu Project, Compile & Run (F5) or the play button on the toolbar.
2. Hover you mouse pointer to the AppStart sub and click on the highlighted link to open the app on your web browser.
   [Spoiler: Open in browser]
3. The browser will open and load the index page.
4. It is a CRUD web application where you can add a new product (C), search for a product (R), edit an existing product (U) and delete a product (D).
5. It has already generated some common APIs following the RESTful API principal.
6. To see the list of APIs, click the API link with a gear icon on top navigation bar.
   [Spoiler: Click the API link]
7. You will be redirected to the API documentation page.
8. You will see there are endpoints such as GET, POST, PUT and DELETE which are represented in different colours.
   [Spoiler: Click to expand the section]
9. You can click on any item to expand the section.

## Perform Tests

### GET
1. Let's try to perform our first GET request with an id parameter.
2. Click on the second end point labeled GET /api/categories/:id
3. Edit the Path by replacing the :id parameter with 2 and click on the green Submit button.
   [Spoiler: Edit the Path]
4. The form will send an AJAX request to the API server and return a JSON response (Code 200 for Success).
   [Spoiler: JSON Response Success]

### POST
1. To test a POST end point, click the third item with labeled POST /api/categories.
2. Copy the sample format from the left and paste in the Body textarea.
   [Spoiler: Make POST request]
3. Edit the content, i.e value of the category name in the illustration and click the yellow Submit button.
   [Spoiler: JSON Response Created]
4. You may follow the convention to return code 201 Created for new item added to the resource.
5. For this demo, it is set to return a simple response and it doesn't support text Created but display success as default text.
6. You need to set Simple Response to False in server configuration to return code 201 response for your client apps.

Next tutorial: [Part #2 Configure Settings](https://github.com/pyhoon/web-api-server-tutorial/blob/main/Part%20%232%20Configure%20Settings.md)
