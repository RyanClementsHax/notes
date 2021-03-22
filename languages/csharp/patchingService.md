# Patching Service

## Main service
``` csharp
public abstract class PatchService<TDbContext, TModel, TEntity> : IPatchService<TModel, TEntity>
    where TDbContext : DbContext
    where TModel : class
    where TEntity : class, ICloneable
{
    protected readonly TDbContext _context;
    protected IEntityMapper<TEntity, TModel> _mapper;

    public PatchService(TDbContext context, IUserContext userContext, IEntityMapper<TEntity, TModel> mapper)
    {
        _context = context;
        _mapper = mapper;
    }

    public async virtual Task<IPatchedEntity<TEntity>> Patch(TEntity existingEntity, TModel model, bool isNew = false)
    {
        try
        {
            _mapper.UpdateEntity(existingEntity, model, IsUserAuthorized);
        }
        catch (UpdateEntityAuthenticationException e)
        {
            return new PatchedEntity<TEntity> { Errors = new[] { e.Message }, IsUnauthorized = true };
        }

        if (isNew)
            _context.Set<TEntity>().Add(existingEntity);

        await SaveChangesAsync();

        return new PatchedEntity<TEntity>{ Entity = existingEntity };
    }
}
```

## Mapping Service

``` csharp
public abstract class EntityMapper<TEntity, TModel> : IEntityMapper<TEntity, TModel>
    where TEntity : class, ICloneable
    where TModel : class
{
    /// <summary>
    /// Maps the given name of the field in the model to both 
    ///     1. the name of the field in the entity object
    ///     2. a transformation to be done on the object before it is assigned to the entity
    /// Defaults to no trasformation and the name being the same in both the model and the entity.
    /// </summary>
    /// <param name="modelFieldName">The name of the field in the model</param>
    /// <returns>The name of the field in the entity that coresponds to the given name of the field in the model and a function that trasforms the value.</returns>
    protected virtual (string, Func<object, object>) MapFields(string modelFieldName, TEntity entity, TModel model)
        => (modelFieldName, x => x);

    /// <summary>
    /// A class that updates the given entity with values from the model based on the mapping defined in the MapFields function
    /// </summary>
    /// <param name="entity">The entity to be updated</param>
    /// <param name="model">The model with the data to update the entity with</param>
    /// <param name="authCheck">An optional function that takes in the field name and returns a boolean indicating if the field is authorized to be edited</param>
    /// <returns>A refrence to the updated entity</returns>
    public TEntity UpdateEntity(TEntity entity, TModel model, Func<string, bool> authCheck = null)
    {
        foreach (var prop in model.GetType().GetProperties().Where(x => x.PropertyType.IsGenericType && x.PropertyType.GetGenericTypeDefinition() == typeof(Field<>)))
        {
            var value = (IField)prop.GetValue(model);
            if (value?.IsUpdated ?? false)
            {
                var (entityFieldName, entityTransformation) = MapFields(prop.Name, entity, model);

                if (!(authCheck?.Invoke(prop.Name) ?? true))
                    throw new UpdateEntityAuthenticationException(entityFieldName);

                entity.GetType().GetProperty(entityFieldName)?.SetValue(entity, entityTransformation(value.UpdatedValue));
            }
        }

        return entity;
    }

    public TModel UpdateEntity(TModel entity, TEntity model, Func<string, bool> authCheck = null)
    {
        throw new NotImplementedException();
    }
}
```

## Helper classes

``` csharp
public class UpdateEntityAuthenticationException : Exception
{
    public UpdateEntityAuthenticationException(string fieldName) : base($"You are unauthorized to edit the field {fieldName}.") { }
}

public interface IField
{
    bool IsUpdated { get; }
    object UpdatedValue { get; }
}

public class Field<T> : IField
{
    public bool IsUpdated { get; set; }
    public T UpdatedValue { get; set; }

    object IField.UpdatedValue => UpdatedValue;
}
```