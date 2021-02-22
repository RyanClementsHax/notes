# Entity Framework

## Code first

### Ensuring a db created on startup
- [ref](http://www.binaryintellect.net/articles/87446533-54b3-41ad-bea9-994091686a55.aspx)
```cs
public void Configure(IApplicationBuilder app, IWebHostEnvironment env, AppDbContext db)
{
    // ...

    db.Database.EnsureCreated();

    // ...
}
```

### Migration
```bash
dotnet ef database update last_migration_name --startup-project your_database_project --verbose
dotnet ef migrations remove --startup-project your_database_project
dotnet ef migrations add new_migration_name --startup-project your_database_project
dotnet ef database update --startup-project your_database_project --verbose
```

### Issue with migration script generation

Due to an issue in the ef-core cli tool, the --idempotent flag forces the IF statement making the changes idempotent cannot have a batch inside it. This is normally fine but for ALTER FUNCTION statements that need to be in their own batch, this causes an issue. The solution is to put the in a dynamic sql query.

Fails
```cs
protected override void Up(MigrationBuilder migrationBuilder)
{
    var sp = @"
        ALTER FUNCTION [dbo].[thing]
        (
            @ContactID INT = NULL,
            @OperationID INT = NULL
        )
        ...
        ";

    migrationBuilder.Sql(sp);
}
```

Works
```cs
protected override void Up(MigrationBuilder migrationBuilder)
{
    var sp = @"
        DECLARE @Sql NVARCHAR(MAX)
        SET @Sql = '
        ALTER FUNCTION [dbo].[thing]
        (
            @ContactID INT = NULL,
            @OperationID INT = NULL
        )
        ...'
        EXEC(@Sql)
        ";

    migrationBuilder.Sql(sp);
}
```

### SQLite issue
- put this in the context class to avoid errors with the date format for `SQLite`
    - `SQLite` is handy when you want to use it for in memory unit testing
    ```cs
    if (Database.ProviderName == "Microsoft.EntityFrameworkCore.Sqlite")
    {
        modelBuilder.Entity<EntityWithDateTimeOffsetProperty>()
            .Property(v => v.CreatedAt)
            .HasConversion(new DateTimeOffsetToBinaryConverter());
    }
    ```
    or more generically
    ```cs
    if (Database.ProviderName == "Microsoft.EntityFrameworkCore.Sqlite")
    {
        foreach (var entityType in modelBuilder.Model.GetEntityTypes())
        {
            var properties = entityType.ClrType.GetProperties()
                .Where(p => p.PropertyType == typeof(DateTimeOffset)
                            || p.PropertyType == typeof(DateTimeOffset?));

            foreach (var property in properties)
            {
                modelBuilder
                    .Entity(entityType.Name)
                    .Property(property.Name)
                    .HasConversion(new DateTimeOffsetToBinaryConverter());
            }
        }
    }
    ```

## DB first
```PowerShell
Scaffold-DbContext "Data Source=your_db_server_host_name; Integrated Security=True;MultipleActiveResultSets=True; Initial Catalog=your_db" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Table1","Table2" -f
```
- `-f` is for overriding existing class files generated previously


## Handling migrations
- [there are many ways to create migrations](https://www.thereformedprogrammer.net/handling-entity-framework-core-database-migrations-in-production-part-1/#1a-standard-ef-core-c-migration-script) each has tradeoffs
- [there are many ways to apply migrations](https://www.thereformedprogrammer.net/handling-entity-framework-core-database-migrations-in-production-part-2/) each has tradeoffs
