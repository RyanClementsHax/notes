# SQL

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

## `sys.databases`
- contains information about all the databases on the server
- can be querried like any other table

    ```sql
    select *
    from sys.databases
    ```

## Strings

## Escape char
- `'`, its a single quote
    ```sql
    '''hello''' -- 'hello'
    ```
