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
- you can only create one at a time by design
- the snapshot can be found like any other db in `sys.databases` but it will reference the db it was created off of in the `source_database_id` column
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
    - conditionally restoring is pretty simple, but should be broken up over a few queries as there is no support to do this in one statement
    - if the condition is to only apply an update if the db has been modified since taking the snapshot, you have to do a light hack to get this working
      - there is no date field that stores when a snapshot was applied
      - you would need to create your own table
      - if that is not an option, you can take the last write date that happened to a database and compare that with when the snapshot was created
      - the following query can be used to check this, but sql server will wipe the last right time on server restart, so on restart it would be ideal to restore the snapshot (cuz there is no other way of telling if the database has been modified since that I know of) and then recreate the snapshot (which is instant) so you can update the create data
        ```sql
          select dbs.name as catalogName, snapshots.name as snapshotName, snapshots.create_date as createDate,
          case
              when snapshots.name is null then null
              when max(last_user_update) is null and (select sqlserver_start_time from sys.dm_os_sys_info) < snapshots.create_date then 1
              when max(last_user_update) < snapshots.create_date then 1
              else 0
          end as isInSyncWithCatalog
          from sys.databases dbs
          left join sys.databases snapshots on dbs.database_id = snapshots.source_database_id
          left join sys.dm_db_index_usage_stats stats on dbs.database_id = stats.database_id
          where dbs.source_database_id is null
          group by dbs.name, snapshots.name, snapshots.create_date
        ```
          - it is important to do `max(last_user_update)` because there could be many stats rows for the same db snapshot pair in this query
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

## Multiple Active Result Sets (MARS)
- [example](https://docs.microsoft.com/en-us/sql/connect/ado-net/sql/manipulate-data?view=sql-server-ver15)
- allows for concurrent querries on one sql connection
- _**not thread safe**_
- needs to explicitly be set in the connection string by setting `MultipleActiveResultSets=True`

## Check if db exists
```sql
declare @dbName nvarchar(max) = 'my_db'

if (
    exists (
        select name 
        from master.dbo.sysdatabases 
        where '[' + name + ']' = @dbName 
        or name = @dbName
    )
)
begin
    print('exists');
end
else
begin
    print('doesnt exist');
end
```

## `sys.databases`
- contains information about all the databases on the server
- can be querried like any other table

    ```sql
    select *
    from sys.databases
    ```

## `@@Identity`
- returns the most recently generated identity value
    ```sql
    select max(id) as MaxUsedIdentity
    from EmployeeData
    -- lets say this outputs 110

    insert into EmployeeData values ('Raj')
    -- generates id of 111

    select max(id) as MaxUsedIdentity
    from EmployeeData
    -- 111

    select @@Identity
    -- 111
    ```

## `sys.dm_db_index_usage_stats`
- holds some stats on the databases on the server

## Managing db size
- [ref](https://aboutsqlserver.com/2014/12/02/size-does-matter-10-ways-to-reduce-the-database-size-and-improve-performance-in-sql-server/)
  1. reducing index fragmentation
  2. implementing data compression
  3. removing unused indexes
  4. removing redundant indexes
  5. implementing filtered indexes
  6. using appropriate data types
  7. storing LOB data outside of the database
  8. compressing LOB data in the database
  9. storing data in clustered columnstore indexes
  10. reducing amount of free space in the database
- when creating a catalog, you can specify the file size
  - [ref](https://stackoverflow.com/questions/51371188/create-sql-server-database-with-defined-file-size)
- you can also reduce file sizes via sql
  - [ref](https://dba.stackexchange.com/questions/134524/reduce-mdf-file-size)
  - warning: this may lose some data or make things funky if you go about this way, but I could be wrong
  ```sql
  dbcc shrinkfile(file_name, percentage_to_shrink_to)
  ```

## `xp_cmdshell`
- this is an extension that allows you to send handy commands through sql to manage the server
- this is only available on the windows version, [not the linux version](https://stackoverflow.com/questions/59971345/cannot-enable-xp-cmdshell-on-sql-server-2017-express-on-linux)
- checking if enabled
  ```sql
  select convert(int, isnull(value, value_in_use)) as config_value
  from  sys.configurations
  where name = 'xp_cmdshell' ;
  ```
- enabling this via sql
  ```sql
  print 'Enabling Advanced Options'
  exec sp_configure 'show advanced options', 1
  go

  print 'reconfigure'
  reconfigure -- these are needed after changing settings on the db for them to take effect
  go

  print 'Enabling xp_cmdshell'
  exec sp_configure 'xp_cmdshell', 1
  go

  print 'reconfigure'
  reconfigure
  go
  ```

## Duplicating dbs
- cannot do it with snapshots because snapshots can only be used for the db they were created from
- you can do this with backups, but need shell access to the server to copy and rename the mdf files
  - this is one place where enabling `xp_cmdshell` comes in handy
- [ref](https://serverfault.com/questions/62590/how-to-duplicate-mssql-database-on-the-same-or-another-server)
