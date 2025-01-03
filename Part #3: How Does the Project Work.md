Part #3: How Does the Project Work?
First thing to know, this is a B4J server app. Therefore, it depends on the B4J Server library.
You also need to understand, it is a non-UI or console app. It doesn't have native UI library or component.
You can't add any B4J library that depends on XUI or jFX.
After the project is compiled as Release, you can run the binary file on Windows CMD or Terminal on Linux.
To host the server app, it is recommended to find a VPS that you have full access to run the binary using a remote SSH terminal.
You also need to have a JDK on the server for you to call the .jar file.
It is recommended that you put the compiled binary file e.g server.jar, together with the www directory inside a location that you have write permission.
The server will have to write it's access log and database file if you are using SQLite to keep inside the location same as the binary file.
You also need to make sure the server can read the config.ini file and contents inside www directory.
You can follow other tutorials about running B4J Server on this forum.
Starting the Server
To start the server, you Initialize the object as global in Main module and call the method Start.
We should configure the settings before we start the server.
The default server port number for B4J server is 8080.
We can change this setting using InitServer sub in the Main module.
Initializing Server Configuration
In this project, we use config.ini to make changes to the server configuration without needing to recompile the project.
The settings in config.ini has been explained in the previous tutorial.
This project has embedded with a config.example file in the assets or Files directory.
If you have deleted the config.ini file, a new config.ini file will be copied from the example template.
InitServer sub will read config.ini file for the configuration settings.
All the configuration will be assigned to a Map object name ctx where it can be read from anywhere of the project and also use by HTML template engine, such as for displaying the title label and version number.
EnableCORS​
This setting must be enabled to allow another application (from different origin) to access our API especially when using JavaScript.
If you have another web app hosted with different port number e.g Vue app running on port 3000 and this server is running on port 8000 then you need to enable CORS or otherwise you will get errors in web browser.
You can disable this setting if you are sure there are no other application sharing the API.
This is not applied to mobile or desktop native client apps want to access the API.
EnableSSL​
This setting is recommended to be set to True during production to protect the data transfer being encrypted between the server and client.
As explained in previous tutorial, you need to configure the SSLPort to number other than 0 and provide information of the keystore.
EnableHelp​
Basically this setting is use to hide the API documentation link to be visible to the end user.
You can also use Conditional symbol to add or disable the HelpHandler during production.
SimpleResponse​
This setting allows you to choose the API output in different JSON format.
By default, it is not enabled.
This means the output of the JSON Response follows a predefined structure as set inside ReturnHttpResponse sub of WebApiUtils library.
Learn more about SimpleResponse in next tutorial.
StaticFilesFolder​
Usually the static files folder is set as www inside Objects during development.
It is recommended to leave the folder name as www or you may want to change as public.
After compiled as Release, it will become the same level as the jar file.
StaticFilesBrowsable​
You may want to restrict the client to list the contents of your www assets directory from the browser.
You can set StaticFilesBrowsable to False which is by default also set as False.
B4X:
Config.StaticFilesFolder = File.Combine(File.DirApp, "www")
Config.StaticFilesBrowsable = False
Version​
The VERSION_NAME is set in Process_Globals which is just for displaying purpose in front end and terminal log as "welcome message".
B4X:
Config.Version = VERSION_NAME
Applying Server Configuration (removed)
The ApplySettings sub will take care of applying the settings if you have override any inside InitServer sub.
You must not modify any code inside ApplySettings sub.
Starting from v3.10, this sub is no longer needed.
Initializing Database Configuration
The InitDatabase sub will take care of applying the settings read from the config.ini file.
You must not modify any code inside InitDatabase sub.
Connecting to the Database
Based on the Build Configuration that you selected, the project is connecting to SQLite by default.
You need to select MySQL from the dropdown list if you want to use MySQL database.
CreateConnection sub will take care of applying the settings and attempt to connect to the database.
It will also check if database is exist or else call CreateDatabase sub.
Creating the Demo Database
If no database is present, the CreateDatabase sub will create a new database, generate the tables and insert some dummy data.
As this template is using MiniORMUtils library, the same code is use to accomplish this task, regardless of whether you choose SQLite or MySQL.
The SQL queries are handled internally by the library despite the differences between the 2 databases.
You may want to learn about using MiniORMUtils in another tutorial or post a new question on this library.
Configure Cross Origin (CORS)
If you have set Config.EnableCORS = True, then you may want to configure the Cross Origin settings on this server accessed from another origin or server inside the ConfigureCORS sub.
This is the case when you have another application that is using JavaScripts to make API calls to this server.
By default, there is already an example added for you.
B4X:
Paths.Add(CreateMap("path": "/api/*", "origins": "http://localhost, http://127.0.0.1:3000", "methods": "POST,PUT,DELETE", "headers": "*"))
This mean you are allowing another application specified in the list of "origins" to access our /api "path" and its derivatives using the "methods" POST, PUT and DELETE.
Configure Port for the Server
The ConfigurePort sub will take care of setting up the server port, ssl keystore, https filter and construct the server url.
You must not change any code in this sub.
This sub will display some messages in the Logs depending on the settings for EnableSSL and the settings in config.ini file.
Configure Static Files
The ConfigureStaticFiles sub will take care of setting up the directory name and browse permission of static file folder.
You must not change any code in this sub.
Switching of Database
If you have decided to switch to another database by selecting a different item from the Build Configurations dropdown list, make sure you have commented the SQLite configuration explained in the previous tutorial Part #2.
If the configuration does not match, the ShowBuildConfigurationNotMatch sub will display a warning message and the application will be terminated immediately.
Commonly Use Subs
You can add extra subs to the Main module so they are reusable in any of the server handler classes.
There are 3 subs added for you for communicating with the database.
Note that DBConnector is part of the MiniORMUtils library.
B4X:
Public Sub DBEngine As String
    Return DBConnector.DBEngine
End Sub

Public Sub DBOpen As SQL
    Return DBConnector.DBOpen
End Sub

Public Sub DBClose
    DBConnector.DBClose
End Sub


[link to next tutorial]
