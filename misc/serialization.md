# Serialization

- when using a strongly typed language like C# sometimes you have the choice to deserialize something as an enum or a class
- in C#, you cannot add properties to an enum because they are represented as integers under the hood, so even if only one of the possible types has a property, in order to keep safety in type constraints, you need to use polymorphism

## Standardizing serialization

- some languages implement their own way to serialize objects like java
- this is problematic if you want to interop with other services or languages
  - also if you want to change to a different stack

## Base 64 encodings

- this is typically used as the cheap way to get any data represented in ASCII
- this increases the size of binary data by 33% though
