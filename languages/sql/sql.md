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

## Idempotent Scripts

- Can be useful if a script needed to be able to be run more than once but only have a single effect
- Many ways to do this but here is one of the easiest 

    ```sql
    IF NOT EXISTS (SELECT 1 FROM SOME_TABLE WHERE ID = 123)
    BEGIN

        INSERT INTO SOME_TABLE
        (ID, stuff, stuff2, stuff3)
        VALUES
        ('123', 'Foo', 'Barr', 'Bang');

    END
    ```