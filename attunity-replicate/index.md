# Attunity Replicate

AWS RDS currently does not support MS SQL Server CDC on Standard version, only Enterprise. Attunity supports query based CDC. Attunity will query a table based on a specified column (usually a timestamp, or incremental number) and pull the latest rows that need to be replicated. It also uses another column on the table called "Action". It will either have "I", "U", or "D". This tells Attunity to insert, updated, or delete the record in the replicated database.

# Access Admin Interface

Attunity Replicate is installed on a server. The admin interface is also installed on the local server which cannot be accessed, by default, from outside the server.

- Remote Desktop into the server
- Go to URL, https://{ec2-servername}/attunityreplicate/login/
- Put in the EC2 Remote Desktop login credentials
    - L: Administrator
    - P: xxxxxxxxxxxxx

# Manage Endpoint Connections

To replicate between database servers, sources and endpoints need to be setup first. 

## AWS RDS Aurora

Download and install the [MySQL ODBC Driver](https://cdn.mysql.com//Downloads/Connector-ODBC/5.3/mysql-connector-odbc-5.3.10-winx64.msi) if the driver does not exist.

In Attunity:

- Select, "New Endpoint Connection"
- Enter in a name for the connection
- Choose "Source" or "Target"
- Select Type, "MySQL"
- Enter into "Server Name" the hostname or IP of the server
- Enter into "User name" the username for the database server
- Enter into "Password" the password for the database server

## AWS RDS SQL Server (Query Based CDC)

SQL Server Native Client must be installed to use this method. Download and install the [SQL Server Native Client](https://download.microsoft.com/download/B/E/D/BED73AAC-3C8A-43F5-AF4F-EB4FEA6C8F3A/ENU/x64/sqlncli.msi)

### Create a System Data Source

In Windows:

- Search for "ODBC data sources (64-bit)"
- Select the tab, "System DSN"
- Select the button, "Add"
- Select from the list, "SQL Server Native Client 11.0"
- Select the button, "Finish"
- Enter into "Name", a name for the DSN Source
- Enter into "Server" the hostname or ip of the database server
- Select "SQL Server Authentication" using a login ID and password entered by the user" for the option of how SQL Server should verify the authenticity of the login ID
- Select the button, "Next"
- Select the button, "Next", and don't change any of the options
- Select the button, "Next", and don't change any of the options
- Select the button, "Finish"

You can test or select "OK" to finish the process.

In Attunity:

- Select, "New Endpoint Connection"
- Enter in a name for the connection
- Choose "Source" or "Target"
- Select Type, "ODBC with CDC"
- Select the option, "DSN", and select the button, "Browse"
- Select the System DSN for the database server
- Enter into "User name" the user name for the database server
- Enter into "Password" the password for the database server
- Select the button, "Save"
