# Records

## Applying attributes to properties
you might come by a situation where you want to write this, but it doesn't take advantage of c# 9's fancy syntax features
```cs
public record User  
{  
    [JsonProperty("User")]  
    public string Name { get; init; }  
  
    [JsonProperty("DateOfBirth")]  
    public DateTime Dob { get; init; }  
} 
```
we would prefer something like this, but it doesn't compile because technically you are applying the attribute to the constructor parameter, not the underlying property that gets generated
```cs
// This is wrong - it sets Attributes to the constructor parameter  
public record User([JsonProperty("User")]string Name,[JsonProperty("DateOfBirth")]DateTime DOB);
```
solution: use `property:` in the attribute to specify that it applies to the underlying property
```cs
public record User(
    [property:JsonProperty("User")] string Name,
    [property:JsonProperty("DateOfBirth")] DateTime DOB
);
```

## Complex properties
- records don't override the default equality comparators for its properties so two records with the same list items in two different list references won't be equal
- [this article](https://www.romanx.co.uk/posts/playing-with-my-record-collection) describes the ugly way around this (requires overriding the equality and hash code operators)

## Equality
- the equality overloads leads to a [performance hit](https://gmanvel.medium.com/interesting-performance-implications-of-c-9-records-equality-check-f0d0a3612919) as expected