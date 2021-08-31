# Configuration

## Configuring entities

- when bulk configuring using `IEntityTypeConfiguration<MyEntity>` you can use `builder.ApplyConfigurationsFromAssembly(GetType().Assembly);` in the `OnModelCreating` method to bulk register all of the types it finds in the assembly
