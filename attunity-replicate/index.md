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

Download and install the [MySQL ODBC Driver](https://cdn.mysql.com//Downloads/Connector-ODBC/5.3/mysql-connector-odbc-5.3.10-winx64.msi)

- Select, "New Endpoint Connection"
- Enter in a name for the connection
- Choose "Source" or "Target"
- Select Type, "MySQL"
- Enter into "Server Name" the hostname or IP of the server
- Enter into "Username" the username for the database server
- Enter into "Password" the password for the database server

## AWS RDS SQL Server

Currently 
