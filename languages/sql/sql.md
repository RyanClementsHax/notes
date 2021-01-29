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