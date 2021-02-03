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