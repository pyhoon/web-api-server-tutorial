# Part #6.3: Adding Database Table
1. Often time we need to add a new table after the database is created.
2. To do it, we need to check whether a table is exist then we create the table.
3. For e.g we want to add a new table tbl_users.

## TableExists sub
1. Let's create a sub for checking the table in Main module.
2. We will pass the table name into this sub.
3. The sub will return True if the table exist or otherwise False.
```B4X
Private Sub TableExists (TableName As String) As Boolean
    Dim MDB As MiniORM
    MDB.Initialize(DBOpen, DBEngine)
    #If MySQL
    Return MDB.TableExists2(TableName, ctx.Get("DbName"))
    #Else
    Return MDB.TableExists(TableName)
    #End If
End Sub
```
## Create Table
1. We will add the code for creating table inside CreateConnection sub.
```B4X
If DBFound Then
    LogColor($"${cnn.DBType} database found!"$, COLOR_BLUE)
    ' Add code for checking table exist
    ...
Else
    LogColor($"${cnn.DBType} database not found!"$, COLOR_RED)
    CreateDatabase
End If
```
2. We use the sub we have created previously to check for the table name.
```B4X
If TableExists("tbl_users") = False Then
```
3. Then we can use MiniORM to create the table.
```B4X
Dim MDB As MiniORM
MDB.Initialize(DBOpen, DBEngine)
MDB.UseTimestamps = True
MDB.Table = "tbl_users"
MDB.Columns.Add(MDB.CreateORMColumn2(CreateMap("Name": "user_name")))
MDB.Columns.Add(MDB.CreateORMColumn2(CreateMap("Name": "user_email")))
MDB.Columns.Add(MDB.CreateORMColumn2(CreateMap("Name": "user_password")))
MDB.Create
MDB.Execute
LogColor($"Table tbl_users is created!"$, COLOR_BLUE)
```
4. After calling the Create method, we call Execute to execute the SQL command.
5. The new table will be created when we run the project again.
   The complete code is as following:
```B4X
Private Sub CreateConnection
    Try
        LogColor("Checking database...", COLOR_BLUE)
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
        #If MySQL
        Wait For (DBConnector.DBExist2) Complete (DBFound As Boolean)
        #Else
        Dim DBFound As Boolean = DBConnector.DBExist
        #End If
        If DBFound Then
            LogColor($"${cnn.DBType} database found!"$, COLOR_BLUE)
            ' Check table exist
            If TableExists("tbl_users") = False Then
                Dim MDB As MiniORM
                MDB.Initialize(DBOpen, DBEngine)
                MDB.UseTimestamps = True
                MDB.Table = "tbl_users"
                MDB.Columns.Add(MDB.CreateORMColumn2(CreateMap("Name": "user_name")))
                MDB.Columns.Add(MDB.CreateORMColumn2(CreateMap("Name": "user_email")))
                MDB.Columns.Add(MDB.CreateORMColumn2(CreateMap("Name": "user_password")))
                MDB.Create
                MDB.Execute
                LogColor($"Table tbl_users is created!"$, COLOR_BLUE)
            End If
        Else
            LogColor($"${cnn.DBType} database not found!"$, COLOR_RED)
            CreateDatabase
        End If
    Catch
        LogError(LastException.Message)
        LogColor("Error checking database!", COLOR_RED)
        Log("Application is terminated.")
        ExitApplication
    End Try
End Sub
```

Next tutorial: [Part #6.4 Adding Table Field](https://github.com/pyhoon/web-api-server-tutorial/blob/main/Part%20%236.4%20Adding%20Table%20Field.md)
