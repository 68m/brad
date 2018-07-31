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

### Required Extra Columns

For any tables that will be replicated, there needs to be 2 columns setup. One is a column that can be sorted in descending order, timestamp. The other is the operation performed on the column. The operation column will specify what operation was applied to the row on that specific time it was updated/deleted/etc.

Add operation column:

```
ALTER TABLE Add Operation VARCHAR(1)
```

To capture the operation a trigger can be created on the table.

```
CREATE TRIGGER dbo.[{table}_Operation]
ON dbo.{table}
AFTER INSERT, UPDATE, DELETE
AS 
BEGIN
    SET NOCOUNT ON;

    DECLARE @Operation as char(1);

    SET @Operation = 'I';
    
    IF EXISTS(SELECT * FROM DELETED)
        BEGIN
            SET @Operation = 
                CASE
                    WHEN EXISTS(SELECT * FROM INSERTED) THEN 'U'
                    ELSE 'D'
                END

                
        END
    ELSE 
        IF NOT EXISTS(SELECT * FROM INSERTED) RETURN; -- Nothing updated or inserted.

    UPDATE 
        {table} 
    SET 
        Operation = @Operation 
    FROM 
        Inserted
    WHERE
        Inserted.Id = {table}.Id

END
```

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

### Create a New Connection

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

# Setup New Replication Task

## Create Replication Task

- Select the tab, "Tasks"
- Select the button, "New Task"
- Enter into "Name" the name of the task
- Select if it is unidirectional or bidirectional
- Task Options
    - Full Load - Remove any data that exists on the target server, and do a full refresh the tables with the source
    - Apply Changes - Replicate any changes in real time from the source to target
    - Store Changes - Save any changes to the local Attunity server
- For most cases, select "Full Load" and "Apply Changes"

## Setup Source and Target for Replication

- Select from the Endpoints sidebar on the left, the source server, and drag it on top of the circle labeled "Drop source server here"
- Select from the Endpoints sidebar on the left, the target server, and drag it on top of the circle labeled "Drop target server here"

## Select Tables to Replicate

This will select and prioritize which tables are replicated. Tables can be prioritized but only 7 levels deep. There are specific priorities:

- Highest Priority
- Higher Priority
- High Priority
- Normal Priority
- Low Priority
- Lower Priority
- Lowest Priority

### To add and remove tables:

- Select the button, "Table Selection"
- Enter into "Schema", the schema (SQL Server) or database name (MySQL/Aurora)
- Enter into "Table", the name of the table, or part of the table name and using % as wildcards
    - e.g.: %cust%
- Select the table(s) from "Table List" on the left side, and move to the right by hitting the button, ">>"
- Tables can be removed by selecting tables from the right side from "Selected Tables", and move them back by hitting the button, "<<"
- Priority can be set in the "Selected Tables" section, and selecting from the Priority drop down for each table

# Run a Task

When starting a task for the first time, the tables should be reloaded from source to destination targets. After the initial load of data, whenever data is inserted, updated or deleted, it will be replicated in real time from source to destination.

- Select the small down arrow next to the button "> Run"
- A dropdown will appear, select the option "Reload Target"
- Select the button "Yes" to reload the target tables

# Enable RDS SQL Server for CDC

- exec msdb.dbo.rds_cdc_enable_db 'database_name'


--Begin tracking a table
exec sys.sp_cdc_enable_table   
    @source_schema           = N'dbo',
    @source_name             = N'BusinessType',
    @role_name               = N'CDCRole'
