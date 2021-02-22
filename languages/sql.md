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
- If you are in a highly concurrent env where this needs to happen in a transaction, Microsoft has a good article on this: https://devblogs.microsoft.com/azure-sql/the-insert-if-not-exists-challenge-a-solution/

## Strings

### Escape char
- `'`, its a single quote
    ```sql
    '''hello''' -- 'hello'
    ```

## case statement
- can be used to conditionally return something in a select clause
```sql
select OrderID, Quantity,
case
    when Quantity > 30 then 'The quantity is greater than 30'
    when Quantity = 30 then 'The quantity is 30'
    else 'The quantity is under 30'
end as QuantityText
from OrderDetails;
```

## Table variables
- there isn't a way in sql to declare an array so you have to use a table
```sql
declare  @tab table(FirstName  varchar(100))
insert into @tab   values('John'),('Sarah'),('George')

select * 
from @tab
where 'John' in (FirstName)
```
