# SQL

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
