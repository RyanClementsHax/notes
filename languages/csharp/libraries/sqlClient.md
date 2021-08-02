# Sql Client

- `System.Data.SqlClient`

## Connection pooling

- by default, it pools connections
- set `Pooling=false` in the connection string if you want to turn this off
- you can also clear the pool related to a connection string using this

    ```cs
    SqlConnection.ClearPool(new SqlConnection(connectionString));
    ```
