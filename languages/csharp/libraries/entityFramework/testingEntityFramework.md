# Testing Entity Framework

## SQLite issues

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

## Context mocking

Sometimes, there are parts of the context that need mocking out because it does not make sense in sqlite land or is not supported in sqlite. In those situations, its a good idea to create a mock context that overrides the necessary things so that it cane sufficeantly tested. Here is an example:

``` cs
public class MockYourDbContext : YourDbContext
{
    public DbConnection OpenConnection { get; set; }

    public MockYourDbContext(DbContextOptions options, SomeOtherThing thing)
        : base(options, thing) { }

    //Helper class for getting everything set up
    public static MockYourDbContext CreateSQLiteMock(SomeOtherThing thing)
    {
        var instance = new MockYourDbContext(
            new DbContextOptionsBuilder<YourDbContext>()
            .UseSqlite("Data Source=:memory:")
            .Options, thing);

        //This needs to stay alive to keep the database alive and persistent for th length of the test
        instance.OpenConnection = instance.Database.GetDbConnection();
        instance.OpenConnection.Open();

        instance.Database.EnsureCreated();
        return instance;
    }

    public override void Dispose()
    {
        OpenConnection.Dispose();
        base.Dispose();
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        //This can be needed if "dbo.SomeTableName" and "xref.SomeTableName" both existed in the schema
        //Sqlite does nto support schemas so you need to name one of the tables something differently so it can create the sqlite db
        modelBuilder.Entity<SomeTableNameXref>().ToTable("SomeTableNameXref");
    }

    //This is an override for a function that calls a tabled valued function. Since we cant copy in the table valued function script, we
    //we instead mock it out with another table
    public override IQueryable<TableValuedFunctionResult> TableValuedFunction(string somePram)
        => TableValuedFunctionResultValues;

    //This is the table that contains the result from the call to our tabled valued function. Any data in here is returned to the table valued function
    //function call so that we can control what is returned. This needs to be a table and not just a List because it needs to be queryable by sql for 
    //compounding queries to work. 
    public DbSet<TableValuedFunctionResult> TableValuedFunctionResultValues { get; set; }

    protected override void SetUpFunctions(ModelBuilder modelBuilder)
    {
        //In SetUpFunctions in the YourDbContext, you would set ".Entity<TableValuedFunctionResult>().HasNoKey()" because it is the result of
        //a stored procedure or a table valued function. Overriding that function allows you instead to give it a key so you can mock out that call with a table.
        modelBuilder.Entity<TableValuedFunctionResult>().HasKey(x => new { x.DealID, x.OperationID });
    }
}
```
