Introduction
At the time of writing, MiniORMUtils is version 1.14 and Web API Server 3 is version 3.00.
MiniORMUtils support SQLite and MySQL databases.
We don't need to write different SQL queries to work with SQLite and later migrate to MySQL.

How to Use MiniORMUtils
When we run Web API Server 3 for the first time, a new SQLite database is created by default.
The creation of database is achieved using CreateDatabase sub in Main module.
The project checks whether a database is already existed every time the server is started through CreateConnection sub.
Before we call this sub, the project needs to load configurations from config.ini file through InitDatabase sub.
B4X:
Private Sub InitDatabase
    cnn.Initialize
    cnn.DBDir = ctx.GetDefault("DbDir", "")
    cnn.DBFile = ctx.GetDefault("DbFile", "")
    cnn.DBType = ctx.GetDefault("DbType", "")
    ...
End Sub
No modification is needed on this sub. The correct way to modify the values is through editing the config.ini file.
Let's take a look at CreateConnection sub.
B4X:
Private Sub CreateConnection
    Try
        Log("Checking database...")
        #If MySQL
        Dim DBType As String = "MySQL"
        #Else
        Dim DBType As String = "SQLite"
        #End If
        If cnn.DBType.EqualsIgnoreCase(DBType) = False Then
            ShowBuildConfigurationNotMatch(cnn.DBType)
            Return
        End If
        DBConnector.Initialize(cnn)
        ...
End Sub
The selection of DBType is determined by Build Configurations.
Build Configurations
The "Default" build configuration is selected means the project will utilize SQLite as database.
To use MySQL, select MySQL from the dropdown list.
MySQL is selected
If the selected DBType in Build Configuration does not match the configurations in config.ini, an error message will show and the server will be terminated.
Build configuration does not match!
If the configuration is matched, the project proceeds to initialize the DBConnector object.
B4X:
DBConnector.Initialize(cnn)
Depending on DBType, the project uses DBExist or DBExist2 sub for checking the database is existed.
B4X:
#If MySQL
Wait For (DBConnector.DBExist2) Complete (DBFound As Boolean)
#Else
Dim DBFound As Boolean = DBConnector.DBExist
#End If
If the return value for DBFound is True, a message log will be showed.
B4X:
If DBFound Then
    LogColor($"${cnn.DBType} database found!"$, COLOR_BLUE)
Else
    LogColor($"${cnn.DBType} database not found!"$, COLOR_RED)
    CreateDatabase
End If
DBFound = True
1730567905307.png

DBFound = False
1730568012970.png
Create Database
Depending on DBType, we call DBCreate for creating a new database.
B4X:
Private Sub CreateDatabase
    Log("Creating database...")
    Select cnn.DBType.ToUpperCase
        Case "MYSQL"
            Wait For (DBConnector.DBCreateMySQL) Complete (Success As Boolean)
        Case "SQLITE"
            Wait For (DBConnector.DBCreateSQLite) Complete (Success As Boolean)
    End Select
The database will be created. If it is failed then the sub will be exited.
1730568746563.png
To use MiniORMUtils, we must first initialize it.
We need to pass 2 parameters to the Initialize method.
B4X:
Dim MDB As MiniORM
MDB.Initialize(DBOpen, DBEngine)
If you want to add timestamps fields in your tables, we can set UseTimestamps to True.
B4X:
MDB.UseTimestamps = True
This will add 3 columns (created_date, modified_date and deleted_date) at the last columns to all of the following tables to be created.
If you don't need these columns, omit this line of code.
This setting can be set to True or False between creation of tables.
We will use a batch to execute the queries. Hence we will add every CREATE table query to a batch and execute them by batch.
This is set using the following code:
B4X:
MDB.AddAfterCreate = True
We also want to add some dummy data to the table.
We need to set the INSERT query as batch.
B4X:
MDB.AddAfterInsert = True
Create Table
We tell MiniORM the table name we want to create.
B4X:
MDB.Table = "tbl_categories"
We can add each column by passing an ORMColumn to the Columns list of the MiniORM.
B4X:
MDB.Columns.Add( <ORMColumn> )
We can use CreateORMColumn2 method to create ORMColumn by passing a Map.
B4X:
MDB.CreateORMColumn2( <Map> )
The Map must contains at least a keyvalue pair with the key "Name".
B4X:
CreateMap("Name": "category_name")
The complete code to add a table column for table tbl_categories with the name "category_name" is per the following:
B4X:
MDB.Columns.Add(MDB.CreateORMColumn2(CreateMap("Name": "category_name")))
In SQLite, it will add a column of the type TEXT for category_name.
In MySQL, it will add a column of the type VARCHAR for category_name with length 255.
Then we call Create method to tell MiniORM to generate a CREATE query.
Insert Row
We can proceed to use MiniORM to add row to the table.
Since we have specified the table name before, we don't need to specify the table name again.
We just need to specify the column name as array to pass to the Columns property.
B4X:
MDB.Columns = Array("category_name")
Then we can add the value using the method Insert2.
B4X:
MDB.Insert2(Array("Hardwares"))
MDB.Insert2(Array("Toys"))
Execute Batch Query
Previously we have add CREATE and INSERT queries to the MiniORM object using methods such as Create and Insert2.
To execute all the queries, we use ExecuteBatch method.
B4X:
Wait For (MDB.ExecuteBatch) Complete (Success As Boolean)
If Success Then
    LogColor("Database is created successfully!", COLOR_BLUE)
Else
    LogColor("Database creation failed!", COLOR_RED)
End If
We will see a log message showing the execution success or failed.
Finally, we close the database connection using the Close method.
B4X:
MDB.Close
The Close method only applies to MySQL internally. SQLite has no effect but we left the code for compatibility.
This is the end of tutorial. 
