# Serialization

- when using a strongly typed language like C# sometimes you have the choice to deserialize something as an enum or a class
- in C#, you cannot add properties to an enum because they are represented as integers under the hood, so even if only one of the possible types has a property, in order to keep safety in type constraints, you need to use polymorphism