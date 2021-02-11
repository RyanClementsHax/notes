# SQL Server

## Log locations
```
/var/opt/mssql/data/master.mdf
/var/opt/mssql/data/mastlog.ldf
/var/opt/mssql/log/errorlog
```

## Connection Strings

### [Timeouts](https://docs.microsoft.com/en-us/dotnet/api/system.data.sqlclient.sqlconnection.connectiontimeout?view=dotnet-plat-ext-5.0)
- `Connect Timeout` and `Connection Timeout` are the same property
  - `Gets the time to wait (in seconds) while trying to establish a connection before terminating the attempt and generating an error.`
- default is 15 seconds in some libraries
- when set to 0, the connection is set to wait indefinitely
  - this is not the case for all libraries however
  - `System.Data.SqlClient` version `4.8.x` implements this but version `4.7.0` times out immediately

## SQL Server With Docker

### EULAs
- the env var `ACCEPT_EULA` needs to be set to `Y` when installing the sql server docker container or installing `mssql-tools`, otherwise you will need an interactive terminal to accept it

## Snapshots
- creates a read only version of the db at the time of creation
- you can only create one from what I know
- comparison with backups \
![Comparison with backups](./snapshotVsBackup.png)
- creating
    ```sql
    create database snapshot_name on
    (
        name = name_of_db,
        filename = '/some/path/and/file_name'
    )
    as snapshot OF name_of_db
    ```
    - this is pretty much instant, but every time a page is "dirtied" after the snapshot is created, a copy of it is made
- deleting
    ```sql
    drop database if exists snapshot_name
    ```
- restoring
    ```sql
    restore database name_of_db
    from database_snapshot = snapshot_name
    ```
    - this requires that no other open connections to the database exist
    - if there are, this will wait for those connections to close, but timeout at 20sec and fail
      - be warry of sql connection pooling
    - if you need the connection to be forceably closed when you restore, wrap your restore statement in this
        ```sql
        alter database name_of_db set single_user with rollback immediate

        -- your restore code

        alter database name_of_db set multi_user
        ```
    - the performance of restore scales with the number of pages you "dirty" since creating the snapshot
    - unoptimized, even without "dirtying" pages, the snapshots seem to take 1.5sec each
- snapshots can be optimized a [few ways](https://dba.stackexchange.com/questions/179695/fastest-way-to-restore-a-sql-server-database)

## Instant file initialization
- to save time from zeroing out files on creation (which can greatly speed up operations like snapshots), you can disable the zeroing by turning on instant file initialization
- this is a security risk because the process would be able to see the data that was previously allocated in that area
- to enable this on a windows environment, you need to give sql server certain permissions in the OS
- this doesn't seem to be well supported on its docker container environment though]
- you can check if it is enabled by running this
  ```sql
  select instant_file_initialization_enabled, *
  from sys.dm_server_services
  where servicename like 'SQL Server%'
  ```
- it may be tempting to run something like this to try to enable instant file initialization, but it doesnt work. trust me. I've tried. You need to enable it by doing the things i mentioned above.
  ```sql
  update sys.dm_server_services
  set instant_file_initialization_enabled = 'Y'
  where servicename like 'SQL Server%'
  ```

## Connections
- you can check all open connections to the catalogs by running the following query
  ```sql
  select db_name(dbid) as DBName, count(dbid) as NumberOfConnections, loginame as LoginName
  from sys.sysprocesses
  where dbid > 0
  group by dbid, loginame
  ```

## Catalogs
- these are the "databases" on the server

### Get all catalogs with a certain name
```sql
select *
from sys.databases
where name like 'pattern'
```
- helpful for getting all of the snapshots on the server (assuming you have some naming scheme)

### Get all catalogs with a certain table
```sql
select name
from sys.databases
where object_id(quotename(name) + '.[dbo].[the_table]', 'U') is not null
```

## Dynamic sql
  1. `exec`
    - this allows for any sql to be run, but doesn't have an option for parameters other than this ugly string addition
    ```sql
    declare @sqlCommand varchar(1000)
    declare @columnList varchar(75)
    declare @city varchar(75)

    set @columnList = 'AddressID, AddressLine1, City'
    set @city = '''London'''
    set @sqlCommand = 'select ' + @columnList + ' from Person.Address where City = ' + @city

    exec (@sqlCommand)
    ```
  2. `sp_executesql`
    - this does everything exec does, but it allows for parameters
    ```sql
    declare @sqlCommand nvarchar(1000)
    declare @columnList varchar(75)
    declare @city varchar(75)

    set @columnList = 'AddressID, AddressLine1, City'
    set @city = 'London'
    set @sqlCommand = 'select ' + @columnList + ' from Person.Address where City = @city'

    execute sp_executesql @sqlCommand, N'@city nvarchar(75)', @city = @city
    ```