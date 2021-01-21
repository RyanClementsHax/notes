# Entity Framework

## Code first

### Migration
```bash
dotnet ef database update last_migration_name --startup-project your_database_project --verbose
dotnet ef migrations remove --startup-project your_database_project
dotnet ef migrations add new_migration_name --startup-project your_database_project
dotnet ef database update --startup-project your_database_project --verbose
```

### SQLite issue
- put this in the context class to avoid errors with the date format for `SQLite`
    - `SQLite` is handy when you want to use it for in memory unit testing
    ```c#
    if (Database.ProviderName == "Microsoft.EntityFrameworkCore.Sqlite")
    {
        modelBuilder.Entity<EntityWithDateTimeOffsetProperty>()
            .Property(v => v.CreatedAt)
            .HasConversion(new DateTimeOffsetToBinaryConverter());
    }
    ```
    or more generically
    ```c#
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
