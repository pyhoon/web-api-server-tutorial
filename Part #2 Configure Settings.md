# Part #2: Configure Settings
1. B4J generate a config.ini when you compile the project for the first time in Debug (or Release) mode.
2. You can open the file using Notepad or any Text Editor.
3. After you made any changes, you need to compile the project again for the settings to take effect.
```B4X
# Define App Constants
HOME_TITLE=WEB API SERVER
APP_TITLE=Web API Server
APP_TRADEMARK=B4X
APP_COPYRIGHT=Copyright Computerise System Solutions 2024
```
4. You can edit these settings.
5. It will applies to the text displayed on the front-end and API documentation page.
6. Similar to config.properties, the values does not support UTF8. You may want to set text for non-English characters in the code.
```B4X
# Server Path
ROOT_URL=http://127.0.0.1
ROOT_PATH=
API_NAME=api
API_VERSIONING=False
```
7. ROOT_URL (default=http://127.0.0.1) is for setting the server URL. You can edit to your domain name in production release.
   *Note: It seems Chrome is no longer allowed http://localhost so it is better to stick with http://127.0.0.1 during development.*
8. ROOT_PATH (default=<empty>) can be set to other name e.g /web
9. API_NAME (default=api) is better to leave as default. You need to change the values in code, html templates and JavaScript files if you edit it.
10. API_VERSIONING (default=False) can be set to e.g /v1 or /preprod
```B4X
# Java server port
ServerPort=8080
SSLPort=0
```
11. It is important to set the correct number for B4J server port.
12. Make sure the port number is not used by another service or blocked by the firewall.
13. You can set the port e.g 80
14. It is recommended to use SSL protocol in production. If SSLPort number is set to 0, SSL is disabled.
15. You can set it to e.g 443
```B4X
# SSL Keystores
# Generate a keystore from Windows CMD
# C:\Java\jdk-11.0.1\bin\keytool -keystore keystore -alias jetty -genkey -keyalg RSA
# Copy keystore file to Objects folder of the B4J project
SSL_KEYSTORE_DIR=/etc/letsencrypt/live/mydomain.com
SSL_KEYSTORE_FILE=keystore
SSL_KEYSTORE_PASSWORD=xxxxxxxxx
```
16. If SSLPort is not set to 0 (enabled), you need to set the keystore settings.
17. During development, you can use CMD to call keytool in the JDK to generate a self sign keystore file.
18. Put this file in Objects folder and leave the SSL_KEYSTORE_DIR setting as <empty>.
19. During production, you can use a real certificate or generate one using LetsEncrypt.
```B4X
# DATABASE CONFIGURATION

## SQLite configuration:
DbType=SQLite
DbFile=webapi3.db
DbDir=
DriverClass=org.sqlite.JDBC
JdbcUrl=jdbc:sqlite:{DbDir}/{DbFile}
```
20. SQLite is set as default DbType.
21. You can change the DbFile as you like e.g server.sqlite
22. You can leave DbDir as <empty> to store the file inside Objects directory.
23. If you want to save the file in a different location, you need to set it using forward slash or escape character e.g C:/server/db or C:\\server\\db
24. It is recommended to leave the values for DbType, DriverClass and JdbcUrl as default.
25. If you want to use MySQL as backend database, uncomment the settings like the following example.
```B4X
## MySQL configuration:
DbType=MySQL
DbName=webapi3
DbHost=localhost
DbPort=
DriverClass=com.mysql.cj.jdbc.Driver
JdbcUrl=jdbc:mysql://{DbHost}:{DbPort}/{DbName}?characterEncoding=utf8&useSSL=false
User=root
Password=password
MaxPoolSize=100
```
26. You can leave DbPort as <empty> if MySQL is running on default port 3306.
27. You can edit the DbName, User, Password, MaxPoolSize or other settings according to your needs.

Next tutorial: [Part #3.0 How Does the Project Work?](https://github.com/pyhoon/web-api-server-tutorial/blob/main/Part%20%233.0%20How%20Does%20the%20Project%20Work.md)
