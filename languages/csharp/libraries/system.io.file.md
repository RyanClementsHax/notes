# System.IO.File

## Testing file system access
- seriously consider using [System.IO.Abstractions](https://github.com/System-IO-Abstractions/System.IO.Abstractions) which puts the entire file system api behind interfaces
- it also has some handy testing utilities to reduce mocking code