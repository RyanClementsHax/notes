# Strongly Typed Strings

## Purpose
- provide a type around a string to allow for easier readability, prevent string mismatch, and allow for extensability specific to the string type
- string mismatch refers to mixing up strings that represent different things

## Example
```cs
public class SqlStr
{
    string Value { get; }
    public SqlStr(string value) { Value = value; }
}
```

## Extensability
- you can put common helper methods on this class
    ```cs
    public class SqlStr
    {
        string Value { get; }
        public SqlStr(string value) { Value = value; }

        public static implicit operator string(SqlStr str) => str.Value;
        public static implicit operator SqlStr(string str) => new SqlStr(str);

        public override string ToString() => Value;
    }
    ```
- you can also implement extension methods specific to a certain type of string